

#### Learnings
1. Supporting multiple devices for multiple users, this might lead to consistency issues, if we define a device to be mapped to a userId, because we store what is the last message stored to a user and that is not same for both devices.
2. Message Buffering on Kafka if too many writes