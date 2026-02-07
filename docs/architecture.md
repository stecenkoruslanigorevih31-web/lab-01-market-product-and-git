
## Product Choice

- Telegram
- telegram.org
- Telegram is a fast, secure, cloud-based messaging app with over 900 million monthly users

## Main components

![Telegram Component Diagram](diagrams/out/telegram/component-diagram/Component%20Diagram.svg)

[Telegram Component Diagram](diagrams/src/telegram/component-diagram.puml)

1. Mobile App (iOS/Android)
This is the client application used by users to send and receive messages and interact with Telegram.
It communicates with Telegram servers using the MTProto protocol.

2. MTProto Gateway (DC Entry)
This component is the entry point for client connections into Telegram data centers.
It receives MTProto requests from clients and forwards them to internal services.

3. Message Handling Service
This service processes incoming messages (sending, receiving, forwarding, etc.).
It is responsible for delivering messages to the correct users or chats.

4. Auth & Session Service
This service manages user authentication and active sessions.
It verifies users and keeps track of their logged-in connections.

5. Media & File Service
This service handles uploading, downloading, and storage of media files (photos, videos, documents).
It interacts with the distributed file system to store and retrieve files.

## Data flow

![Telegram Sequence Diagram](diagrams/out/telegram/sequence-diagram/Sequence%20Diagram.svg)

[Telegram Sequence Diagram](diagrams/src/telegram/sequence-diagram.puml)

What happens in this group

In second group, Alice’s client sends a message to Bob that contains a reference to an already uploaded media file.
The system validates Alice’s session, processes the message, stores it in the database, and assigns a unique message identifier.

Components involved and their communication

Mobile App → MTProto Gateway
The mobile app sends an RPC call sendMessage(peer=Bob, input_file_id_A).
The data exchanged is the target peer (Bob) and the file reference (file_id_A), not the file itself.

MTProto Gateway → Auth Service
The gateway asks the Auth Service to validate Alice’s session.
The exchanged data is Alice’s session / authorization information and a validation result (OK or not).

MTProto Gateway → Message Service
After successful authentication, the gateway forwards the message request to the Message Service.
The data includes sender (Alice), receiver (Bob), message content, and the attached file reference.

Message Service → Sharded DB
The Message Service persists the message into inbox/outbox storage.
The exchanged data is the message record (sender, receiver, text, file reference, timestamps).

Message Service → State / sequence storage (via Data layer)
The service updates the message sequence number (pts).
The data exchanged is the updated sequence counter for the dialog.

Message Service → MTProto Gateway → Mobile App
The Message Service returns that the message is accepted and provides the assigned message ID.
The gateway sends the RPC response back to the client with the message ID and confirmation.

## Deployment

![Telegram Deployment Diagram](diagrams/out/telegram/deployment-diagram/Deployment%20Diagram.svg)

[Telegram Deployment Diagram](diagrams/src/telegram/deployment-diagram.puml)

- I describe the information

## Assumptions

- I assume the cloud storage system implements deduplication to optimize storage costs for shared media files.
- I assume tg is a good messenger.

## Open questions

- What is telegram?
- How to read in chinese?
