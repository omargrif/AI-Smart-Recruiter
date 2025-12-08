# 🤖 n8n AI Smart Recruiter Agent

An autonomous Applicant Tracking System (ATS) workflow built with **n8n**, **Google Gemini**, and **Pinecone**. This agent automates the entire recruitment funnel: sourcing, screening (RAG), scoring, and scheduling.

<img width="1717" height="532" alt="image" src="https://github.com/user-attachments/assets/ff81b116-059e-42a4-98d4-73e96cda37eb" />


## 🚀 Features

- **Email Listening (Gmail):** Automatically detects emails with "Application" or specific keywords and extracts PDF attachments.
- **CV Parsing:** Converts PDF resumes into machine-readable text.
- **Vector Search (RAG):** Uses **Pinecone** to store candidate embeddings and retrieve relevant experience based on the Job Description (JD).
- **AI Scoring (Gemini):** Evaluates candidates on a 0-100 scale based on Hard/Soft skills match.
- **Automated Logic:**
  - **Pass (>75/100):** Sends an interview invitation with a generic calendar link.
  - **Fail (<75/100):** Sends a polite rejection email and archives the profile.
  - 

## 📦 How to Use
[AI Smart Recruiter.json](https://github.com/user-attachments/files/24028425/AI.Smart.Recruiter.json)
{
  "name": "Demo: RAG in n8n",
  "nodes": [
    {
      "parameters": {
        "promptType": "define",
        "text": "=Tu es un Assistant de Recrutement.\nLe recruteur t'envoie une OFFRE D'EMPLOI.\n\nTa mission est de scanner ta base de connaissances ('omar_cv_database') pour identifier TOUS les candidats potentiels dont les profils apparaissent dans les documents récupérés.\n\n--- OFFRE (INPUT) ---\n{{ $json.chatInput }}\n\n--- INSTRUCTIONS ---\n1. L'outil va te renvoyer des extraits de CV mélangés appartenant à plusieurs personnes différentes.\n2. Trie ces informations par candidat (identifie les différents noms).\n3. Pour CHAQUE candidat trouvé, génère une analyse distincte.\n4. Si un candidat ne correspond pas du tout, mets un score faible mais liste-le quand même.\n\nNe te limite pas à un seul candidat. Renvoie une liste.\n--- INSTRUCTIONS DE SORTIE ---\nRemplis le JSON avec un score sur 100, un avis synthétique pour le manager, et une décision finale.\nRÈGLE D'OR : N'utilise jamais de guillemets (\"...\") ou d'apostrophes (') pour encadrer les titres de projets ou les phrases citées dans le champ 'avis_rh'. Laisse le texte brut.\n--- NOUVELLE RÈGLE CRITIQUE ---\nLe champ \"score_compatibilite\" DOIT être un nombre entier SANS guillemets et SANS unité. Exemple: 95.",
        "hasOutputParser": true,
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.agent",
      "typeVersion": 2,
      "position": [
        688,
        -96
      ],
      "id": "579aed76-9644-42d1-ac13-7369059ff1c2",
      "name": "AI Agent"
    },
    {
      "parameters": {
        "mode": "insert",
        "pineconeIndex": {
          "__rl": true,
          "value": "cvv",
          "mode": "list",
          "cachedResultName": "cvv"
        },
        "embeddingBatchSize": 1024,
        "options": {
          "clearNamespace": true
        }
      },
      "type": "@n8n/n8n-nodes-langchain.vectorStorePinecone",
      "typeVersion": 1.3,
      "position": [
        0,
        -96
      ],
      "id": "6abd09ff-e53c-4e98-b4a2-589434476f96",
      "name": "Pinecone Vector Store",
      "credentials": {
        "pineconeApi": {
          "id": "zMPDK5BkXuWPaex4",
          "name": "PineconeApi account"
        }
      }
    },
    {
      "parameters": {
        "dataType": "binary",
        "loader": "pdfLoader",
        "textSplittingMode": "custom",
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.documentDefaultDataLoader",
      "typeVersion": 1.1,
      "position": [
        176,
        64
      ],
      "id": "1349301f-95fa-496e-ab3c-98aa44fa699f",
      "name": "Default Data Loader"
    },
    {
      "parameters": {
        "model": "mxbai-embed-large:latest"
      },
      "type": "@n8n/n8n-nodes-langchain.embeddingsOllama",
      "typeVersion": 1,
      "position": [
        -96,
        80
      ],
      "id": "153c5fa6-3313-4b55-9840-06e0d684e435",
      "name": "Embeddings Ollama",
      "credentials": {
        "ollamaApi": {
          "id": "0ZBS2kFD7nJ6GYrc",
          "name": "Ollama account"
        }
      }
    },
    {
      "parameters": {
        "schemaType": "manual",
        "inputSchema": "{\n  \"type\": \"array\",\n  \"items\": {\n    \"type\": \"object\",\n    \"properties\": {\n      \"nom_candidat\": {\n        \"type\": \"string\",\n        \"description\": \"Nom du candidat identifié.\"\n      },\n      \"score\": {\n        \"type\": \"integer\",\n        \"description\": \"Score sur 100.\"\n      },\n      \"Email\": {\n        \"type\": \"string\",\n        \"description\": \"Boite mail du condidat.\"\n      },\n      \"analyse\": {\n        \"type\": \"string\",\n        \"description\": \"Pourquoi ce candidat matche ou pas.\"\n      }\n    },\n    \"required\": [\"nom_candidat\", \"score\", \"analyse\"]\n  }\n}"
      },
      "type": "@n8n/n8n-nodes-langchain.outputParserStructured",
      "typeVersion": 1.3,
      "position": [
        1024,
        96
      ],
      "id": "23066942-9982-4686-9e4f-f98b2e43df90",
      "name": "Structured Output Parser"
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.lmChatGoogleGemini",
      "typeVersion": 1,
      "position": [
        576,
        96
      ],
      "id": "0e3f2a12-8c86-40fe-94e4-7e8b92777cad",
      "name": "Google Gemini Chat Model",
      "credentials": {
        "googlePalmApi": {
          "id": "ynNAtpfRg6tXknFk",
          "name": "Google Gemini(PaLM) Api account"
        }
      }
    },
    {
      "parameters": {
        "chunkSize": 500,
        "chunkOverlap": 50,
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.textSplitterRecursiveCharacterTextSplitter",
      "typeVersion": 1,
      "position": [
        112,
        192
      ],
      "id": "c9160282-7793-4e4b-81e9-e87edc1e50a6",
      "name": "Recursive Character Text Splitter"
    },
    {
      "parameters": {
        "mode": "retrieve-as-tool",
        "toolDescription": "Utilisez cet outil comme moteur de recherche pour identifier les candidats pertinents dans notre vivier de talents.\n\nCette base contient les CVs complets de plusieurs candidats différents, fragmentés en morceaux sémantiques.\n\nFonction principale : Trouver les extraits de CVs de TOUS les candidats qui correspondent techniquement aux exigences de l'offre (Python, n8n, Supply Chain, etc.).\n\n**Règle d'utilisation :**\n1. Appelez cet outil systématiquement pour sourcer des profils.\n2. L'outil peut renvoyer des mélanges de plusieurs candidats différents : faites attention aux noms pour les distinguer.\n3. Si les documents récupérés ne contiennent pas les mots-clés techniques, considérez que le vivier ne contient pas de profil adapté.",
        "pineconeIndex": {
          "__rl": true,
          "value": "cvv",
          "mode": "list",
          "cachedResultName": "cvv"
        },
        "topK": 30,
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.vectorStorePinecone",
      "typeVersion": 1.3,
      "position": [
        720,
        112
      ],
      "id": "e6d21194-a522-49f9-9abf-988ad92512d3",
      "name": "Pinecone Vector Store1",
      "credentials": {
        "pineconeApi": {
          "id": "zMPDK5BkXuWPaex4",
          "name": "PineconeApi account"
        }
      }
    },
    {
      "parameters": {
        "model": "mxbai-embed-large:latest"
      },
      "type": "@n8n/n8n-nodes-langchain.embeddingsOllama",
      "typeVersion": 1,
      "position": [
        720,
        256
      ],
      "id": "c779b685-3d73-4883-b8cc-359c79efa8f5",
      "name": "Embeddings Ollama1",
      "credentials": {
        "ollamaApi": {
          "id": "tswCFT4UUIVGiDAe",
          "name": "Ollama account 2"
        }
      }
    },
    {
      "parameters": {
        "pollTimes": {
          "item": [
            {
              "mode": "everyMinute"
            }
          ]
        },
        "simple": false,
        "filters": {
          "includeSpamTrash": false
        },
        "options": {
          "downloadAttachments": true
        }
      },
      "type": "n8n-nodes-base.gmailTrigger",
      "typeVersion": 1.3,
      "position": [
        -416,
        -80
      ],
      "id": "6f0c95a0-87c0-487f-b401-538ae0545675",
      "name": "Gmail Trigger",
      "credentials": {
        "gmailOAuth2": {
          "id": "lGlLf8ouSrhtE5f0",
          "name": "Gmail account"
        }
      }
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.chatTrigger",
      "typeVersion": 1.4,
      "position": [
        480,
        -96
      ],
      "id": "479ae39d-a22b-45c0-9241-07bbf2772bcf",
      "name": "When chat message received",
      "webhookId": "1a166a96-d989-4e56-9425-7249d8c1d5af"
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "strict",
            "version": 2
          },
          "conditions": [
            {
              "id": "3d2a3b98-5961-4369-8718-bfe99f985e62",
              "leftValue": "={{ $json.score }}",
              "rightValue": 75,
              "operator": {
                "type": "number",
                "operation": "gt"
              }
            }
          ],
          "combinator": "and"
        },
        "options": {}
      },
      "type": "n8n-nodes-base.if",
      "typeVersion": 2.2,
      "position": [
        1328,
        -128
      ],
      "id": "1229fad7-718f-461b-a7a1-cb1d4934e091",
      "name": "If"
    },
    {
      "parameters": {
        "sendTo": "={{ $json.Email }}",
        "subject": "Bravo",
        "message": "=Bonjour {{ $json.nom_candidat }} (si disponible),    Nous vous remercions de l'intérêt marqué pour le poste d'Ingénieur Automatisation & Data. Votre profil a retenu toute notre attention et nous serions ravis d'échanger avec vous.    Nous souhaitons vous convier à un premier entretien téléphonique/en visio (durée : 30 minutes) pour discuter plus en détail de votre expérience et de notre poste.    Pour planifier cet entretien le plus rapidement possible, veuillez cliquer sur le lien ci-dessous pour choisir l'heure qui vous convient le mieux dans nos agendas :    [Lien de Planification : Voir section 2.1 ci-dessous]    Au plaisir de vous lire et d'échanger avec vous très prochainement.    Cordialement,",
        "options": {
          "appendAttribution": false
        }
      },
      "type": "n8n-nodes-base.gmail",
      "typeVersion": 2.1,
      "position": [
        1584,
        -208
      ],
      "id": "db52661a-8d8b-4d1b-a546-1779a01e1223",
      "name": "Send a message",
      "webhookId": "74a0b504-3916-44fc-812d-7e5c4cc44ecf",
      "credentials": {
        "gmailOAuth2": {
          "id": "lGlLf8ouSrhtE5f0",
          "name": "Gmail account"
        }
      }
    },
    {
      "parameters": {
        "fieldToSplitOut": "output",
        "options": {}
      },
      "type": "n8n-nodes-base.splitOut",
      "typeVersion": 1,
      "position": [
        1040,
        -160
      ],
      "id": "0c9fa2e5-228e-4f0c-b9cf-6ee81b4e6387",
      "name": "Split Out"
    },
    {
      "parameters": {
        "sendTo": "={{ $json.Email }}",
        "subject": "Non",
        "message": "=Bonjour {{ $json.nom_candidat }},\n  Nous vous remercions sincèrement pour l'intérêt que vous avez porté au poste d'Ingénieur Automatisation & Data chez [Nom de l'entreprise] et pour le temps que vous avez consacré à nous envoyer votre candidature.    Après un examen attentif de l'ensemble des profils, nous avons décidé de ne pas donner suite à votre candidature pour ce rôle spécifique.    Votre parcours reste néanmoins intéressant. Nous conservons votre profil dans notre vivier de talents et nous ne manquerons pas de vous recontacter si une opportunité future correspond mieux à vos compétences.    Nous vous souhaitons beaucoup de succès dans vos recherches d'emploi.    Cordialement,   L'équipe Recrutement ",
        "options": {}
      },
      "type": "n8n-nodes-base.gmail",
      "typeVersion": 2.1,
      "position": [
        1552,
        0
      ],
      "id": "ed10d4a9-28d4-4d16-8440-6284b7bde14e",
      "name": "Send a message1",
      "webhookId": "ab0f88f7-d81b-4960-8b06-2445d66b769c",
      "credentials": {
        "gmailOAuth2": {
          "id": "lGlLf8ouSrhtE5f0",
          "name": "Gmail account"
        }
      }
    }
  ],
  "pinData": {},
  "connections": {
    "Default Data Loader": {
      "ai_document": [
        [
          {
            "node": "Pinecone Vector Store",
            "type": "ai_document",
            "index": 0
          }
        ]
      ]
    },
    "Embeddings Ollama": {
      "ai_embedding": [
        [
          {
            "node": "Pinecone Vector Store",
            "type": "ai_embedding",
            "index": 0
          }
        ]
      ]
    },
    "Pinecone Vector Store": {
      "main": [
        []
      ]
    },
    "Structured Output Parser": {
      "ai_outputParser": [
        [
          {
            "node": "AI Agent",
            "type": "ai_outputParser",
            "index": 0
          }
        ]
      ]
    },
    "Google Gemini Chat Model": {
      "ai_languageModel": [
        [
          {
            "node": "AI Agent",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    },
    "Recursive Character Text Splitter": {
      "ai_textSplitter": [
        [
          {
            "node": "Default Data Loader",
            "type": "ai_textSplitter",
            "index": 0
          }
        ]
      ]
    },
    "AI Agent": {
      "main": [
        [
          {
            "node": "Split Out",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Pinecone Vector Store1": {
      "ai_tool": [
        [
          {
            "node": "AI Agent",
            "type": "ai_tool",
            "index": 0
          }
        ]
      ]
    },
    "Embeddings Ollama1": {
      "ai_embedding": [
        [
          {
            "node": "Pinecone Vector Store1",
            "type": "ai_embedding",
            "index": 0
          }
        ]
      ]
    },
    "Gmail Trigger": {
      "main": [
        [
          {
            "node": "Pinecone Vector Store",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "When chat message received": {
      "main": [
        [
          {
            "node": "AI Agent",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "If": {
      "main": [
        [
          {
            "node": "Send a message",
            "type": "main",
            "index": 0
          }
        ],
        [
          {
            "node": "Send a message1",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Split Out": {
      "main": [
        [
          {
            "node": "If",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1"
  },
  "versionId": "811a0a67-281f-4066-9832-ca0f74d93d39",
  "meta": {
    "templateId": "rag-starter-template",
    "templateCredsSetupCompleted": true,
    "instanceId": "bb422bdfddd0dd1ae2ce650bb61ab6d4f537794dec7f7791f9df6ff5fbc64237"
  },
  "id": "gkJyJOKYuQkI6kUC",
  "tags": []
}

1. **Import the Workflow:**
   - Download the `` file from this repository.
   - In your n8n instance, go to **Workflows** > **Import from File**.

2. **Configure Credentials:**
   You will need to set up the following credentials in n8n:
   - **Gmail OAuth2** (for reading emails and sending replies).
   - **Pinecone API** (for vector storage).
   - **Google Gemini API** (for the AI Agent).

3. **Customize the Prompt:**
   - Open the **AI Agent Node**.
   - Edit the System Prompt to fit your specific Job Description or connect it to a Google Doc for dynamic job offers.

## 📄 License

This project is open-source. Feel free to use it for your own automation projects!
