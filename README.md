# Gbrain knowledge bases for personal work and Veracto

An OpenBot agent at Veracto can prepare a client brief using earlier meetings and the current CRM record. The brief links back to both sources, so the team can check what was agreed and see what has changed since the call.

I deployed two [Gbrain](https://github.com/garrytan/gbrain) instances, configured their data sources and connected them to agents through MCP. I also set up the workflow that turns approved meeting transcripts into source-linked notes, adds meeting references to Notion CRM and makes them available to OpenBot.

## Two knowledge bases

![Personal Google Drive files and transcripts go through text extraction and OCR where needed. Work documents and approved recordings go to the separate Veracto deployment. Each Gbrain instance has its own PostgreSQL database and serves its assigned agents via MCP.](assets/gbrain-architecture-detailed.png)

| | Personal | Veracto |
| --- | --- | --- |
| Sources | Google Drive and meeting transcripts | Work documents from Notion and Google Drive, plus approved transcripts or excerpts |
| What it does | Extracts document text, uses OCR for scans and keeps links to originals | Gives agents a searchable record of meetings, agreements and client history |
| Agent access | Personal MCP connection | Separate corporate MCP connection for company work |

The two installations have separate PostgreSQL databases. Full transcripts collected on the personal side stay there. If a transcript includes Veracto work, the personal workflow can pass the approved excerpt to the corporate meeting queue. The corporate meeting registry in Notion can also supply an approved transcript directly.

## From meeting to client brief

![Approved excerpts from the personal source and approved transcripts from the Notion registry enter the Gbrain meeting queue. The worker deduplicates submissions, extracts agreements and checks citations. OpenBot prepares the brief from the meeting notes and the current CRM fields.](assets/meeting-to-client-brief-detailed.png)

1. **Process the transcript.** A background worker inside Gbrain reads the approved text, extracts decisions and agreements, and checks citations against the transcript.
2. **Save the outcome.** Gbrain saves the meeting note with a link to the original. Repeated submissions resolve to the same meeting record. When the client record is confirmed, the worker adds a link to the note in Notion CRM.
3. **Prepare the brief.** OpenBot reads past meetings and agreements from Gbrain and gets current fields from the original Notion CRM record. The brief cites both.

## What I verified

- **Client brief.** An OpenBot agent prepared a brief with references to the original meeting and the current Notion CRM record. The client record was also checked through the corporate Gbrain connection and directly in Notion.
- **Search.** On 25 Russian and English test queries against the corporate knowledge base, the expected source appeared in the first three results for 24 queries.
- **Meeting intake.** A transcript from the personal meeting source was retrieved through the assigned MCP connection. A synthetic mixed meeting exercised the handoff of an approved excerpt to the corporate worker.

This case study documents my deployment, integrations and checks. [Gbrain](https://github.com/garrytan/gbrain) and [OpenBot](https://github.com/nightly-labs/openbot) are upstream projects. The diagrams show the workflow schematically and use no client data.
