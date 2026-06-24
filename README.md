\# Bhairava Protocol



\*\*An autonomous, closed-loop AI content pipeline that researches, generates, publishes, and learns from its own social media performance â€” with zero human intervention.\*\*



Built end-to-end on AWS Lambda, AWS Bedrock (Claude), Google Vertex AI (Veo 3), OpenSearch Serverless, and the Meta Graph API.



\---



\## What it does



Every day, on a fixed schedule, Bhairava Protocol:



1\. \*\*Recalls\*\* â€” queries a vector memory of 500+ past posts and their performance to ground new ideas in what has actually worked

2\. \*\*Ideates\*\* â€” prompts Claude (via AWS Bedrock) to generate a content brief: caption, hashtags, visual direction, hook, and call-to-action, grounded in the retrieved memory

3\. \*\*Generates\*\* â€” converts the brief into a structured video prompt and renders an 8-second video with native audio/dialogue using Google's Veo 3

4\. \*\*Publishes\*\* â€” uploads the video to Instagram as a Reel via the Meta Graph API

5\. \*\*Measures\*\* â€” pulls reach, watch time, saves, and engagement metrics 48 hours later

6\. \*\*Learns\*\* â€” writes a weighted performance score back into memory, so the next cycle's retrieval is informed by what actually landed



This is not a scheduling tool with an AI caption generator bolted on. It is a closed feedback loop: \*\*the system's own published performance becomes training signal for its next decision\*\* â€” a minimal but real implementation of an autonomous agent with memory, action, and reinforcement.



\---



\## Architecture



```

â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”

â”‚                         EventBridge (Cron)                            â”‚

â”‚   09:00 IST daily â†’ orchestrator   â”‚   11:00 IST daily â†’ feedback     â”‚

â”‚              Sunday 02:00 UTC â†’ token refresh                          â”‚

â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜

                             â”‚

                â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”

                â”‚   Lambda: Orchestrator    â”‚

                â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜

                             â”‚

       â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”

       â”‚                      â”‚                          â”‚

â”Œâ”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”      â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”       â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”

â”‚   Phase 1    â”‚      â”‚     Phase 2      â”‚       â”‚     Phase 3      â”‚

â”‚   Memory     â”‚â”€â”€â”€â”€â”€â–¶â”‚    Ideation      â”‚â”€â”€â”€â”€â”€â”€â–¶â”‚   Generation     â”‚

â”‚              â”‚      â”‚                  â”‚       â”‚                  â”‚

â”‚ OpenSearch   â”‚      â”‚ Claude Sonnet 4.6â”‚       â”‚  Veo 3.1 (GCP)   â”‚

â”‚ Serverless   â”‚      â”‚ on AWS Bedrock   â”‚       â”‚  8s video +      â”‚

â”‚ 513 vectors  â”‚      â”‚ RAG-grounded     â”‚       â”‚  native audio    â”‚

â”‚ (Titan       â”‚      â”‚ brief generation â”‚       â”‚                  â”‚

â”‚  embeddings) â”‚      â”‚                  â”‚       â”‚  â†’ S3            â”‚

â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜      â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜       â””â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”˜

       â–²                                                    â”‚

       â”‚                                          â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”

       â”‚                                          â”‚      Phase 4       â”‚

       â”‚                                          â”‚     Actuation       â”‚

       â”‚                                          â”‚                     â”‚

       â”‚                                          â”‚ Meta Graph API v21  â”‚

       â”‚                                          â”‚ S3 â†’ IG container   â”‚

       â”‚                                          â”‚ â†’ publish Reel      â”‚

       â”‚                                          â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜

       â”‚                                                    â”‚

â”Œâ”€â”€â”€â”€â”€â”€â”´â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”         â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”

â”‚            Phase 5                   â”‚         â”‚                     â”‚

â”‚           Feedback                   â”‚â—€â”€â”€â”€â”€â”€â”€â”€â”€â”‚   Live on           â”‚

â”‚                                       â”‚         â”‚   Instagram         â”‚

â”‚  IG Insights API (reach, views,      â”‚         â”‚   @ai.manovigna     â”‚

â”‚  saves, watch time, interactions)    â”‚         â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜

â”‚  â†’ weighted score â†’ DynamoDB         â”‚

â”‚  â†’ re-embedded into OpenSearch       â”‚

â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜

```



\*\*Identities\*\*: The system supports multiple independent "voices" (`manovigna`, `bharathraj33`), each with its own memory namespace, content brief, and Instagram account â€” allowing the same pipeline to run distinct personas in parallel.



\---



\## Tech stack



| Layer | Technology | Purpose |

|---|---|---|

| Compute | AWS Lambda (Python 3.12) | Serverless execution, 3 functions |

| Scheduling | Amazon EventBridge | Cron-based daily/weekly triggers |

| Reasoning | Claude Sonnet 4.6 (AWS Bedrock) | Brief generation, RAG synthesis |

| Vector memory | OpenSearch Serverless | Semantic retrieval over 513 past posts |

| Embeddings | Amazon Titan (multimodal) | Image + text vector representation |

| Video generation | Google Veo 3.1 (Vertex AI) | 8s video with native audio/dialogue |

| Publishing | Meta Graph API v21 | Instagram Reels via Business account |

| Storage | Amazon S3 | Generated video assets |

| State | Amazon DynamoDB | Content briefs, performance scores |

| Secrets | AWS Secrets Manager | All credentials, auto-rotated tokens |



\---



\## Why this is hard (engineering notes)



A working demo of "AI generates a post" is trivial. A \*\*production pipeline that runs unattended on a schedule, across two different cloud providers, with rotating credentials, and recovers from its own performance data\*\* required solving:



\- \*\*Cross-cloud auth in a stateless environment\*\* â€” Vertex AI access tokens are short-lived and normally refreshed via the `gcloud` CLI, which doesn't exist in Lambda's runtime. Solved with a dedicated weekly-refresh Lambda that writes a long-lived token into Secrets Manager, consumed by the orchestrator at invocation time.

\- \*\*OpenSearch Serverless's separate IAM-adjacent access model\*\* â€” data-plane access policies are distinct from IAM roles and have a strict, narrowly-typed permission schema that silently rejects malformed requests with no useful diagnostics.

\- \*\*Reels-specific Insights API\*\* â€” the documented metrics for IG media (`impressions`, `plays`) are rejected for Reels; the correct metric set (`ig\_reels\_avg\_watch\_time`, `ig\_reels\_video\_view\_total\_time`, etc.) is undocumented in most public examples.

\- \*\*Numeric type coercion for DynamoDB\*\* â€” the AWS SDK rejects native Python floats; all performance scores are wrapped as `Decimal`.

\- \*\*Lambda dependency packaging\*\* â€” third-party packages must be installed into the zip's root namespace, not a subdirectory, or imports silently fail at runtime with no build-time warning.

\- \*\*Prompt engineering for Veo 3\*\* â€” a structured prompt template (technical specs / opening / middle / closing / audio direction / strict rules) measurably improved dialogue timing and shot composition versus freeform prompts.



\---



\## Current status



\- âœ… All 6 phases implemented and tested end-to-end

\- âœ… Fully deployed to AWS Lambda + EventBridge â€” \*\*zero manual intervention required\*\* for daily operation

\- âœ… 5 Reels published autonomously to \[@ai.manovigna](https://instagram.com/ai.manovigna)

\- âœ… 513-vector memory bootstrapped from historical content

\- ðŸ”„ GCP service-account-based auth (replacing short-lived user tokens) â€” in progress

\- ðŸ”„ Second identity (`bharathraj33`) â€” memory and ideation ready, publishing pipeline pending



\---



\## Cost



At one post per identity per day:



| Item | Cost |

|---|---|

| Veo 3 generation (\~8s video) | \~$3â€“5 / video |

| AWS (Lambda, Bedrock, OpenSearch, DynamoDB, S3) | \~$0.50â€“1 / day |

| \*\*Total\*\* | \*\*\~$120â€“180 / month\*\* |



\---



\## Repository structure



```

src/

â”œâ”€â”€ ideation\_agent/

â”‚   â””â”€â”€ ideation\_agent.py      # RAG-grounded brief generation (Claude on Bedrock)

â”œâ”€â”€ generation\_layer/

â”‚   â”œâ”€â”€ video\_provider.py      # Veo 3 generation, GCP auth handling

â”‚   â””â”€â”€ caption\_generator.py

â”œâ”€â”€ actuation\_agent.py          # Instagram publishing via Graph API

â”œâ”€â”€ feedback\_agent.py           # IG Insights collection + scoring

â””â”€â”€ orchestrator.py              # Chains all phases, prompt templating



lambda\_orchestrator.py          # Lambda entrypoint â€” daily content cycle

lambda\_feedback.py              # Lambda entrypoint â€” daily performance pull

lambda\_token\_refresh.py         # Lambda entrypoint â€” weekly token rotation

```



\---



\## Setup



> This repository is private and contains no credentials. To run your own instance you will need:

> - AWS account with Bedrock, OpenSearch Serverless, Lambda, DynamoDB, S3, Secrets Manager access

> - GCP project with Vertex AI (Veo 3) enabled

> - Meta Developer app with Instagram Graph API access on a Business account



```bash

git clone <repo>

cd bhairava-protocol

pip install -r requirements.txt

cp .env.example .env   # fill in credentials

python store\_secrets.py  # push to AWS Secrets Manager

python deploy\_lambdas.py  # deploy all 3 Lambda functions

```



\---



\## License



Private repository. All rights reserved.


