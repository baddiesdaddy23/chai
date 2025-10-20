# Project: Chai (Chat + AI)

This repository contains the source code for the "Chai" command-line AI chat application, developed as part of the DBT230 course.

## Author

**Name:** David Gee

## Lab 1: Flat-File Persistence

This lab focuses on building the foundational persistence layer using a simple flat-file (JSON) system. The goal is to establish a performance baseline for file I/O operations, which will serve as a benchmark for subsequent labs involving more advanced database technologies.

## QA
1. What are two different designs you contemplated for your multiple conversations implementation?
-To be honest I only thought of one design and I spent time trying to get it to work that I didn't consider anything else.

2. A vibe coder wants to make a quick MVP (minimum viable product) over the weekend that handles chat threads with AI models. Do you recommend using JSON files for persistence? Why?
-I would say yes because they're fast to start and if its a quick mvp then JSON should be fine.

3. You are interviewing at OpenAI. The interviewer asks if you would use raw JSON files to store user chats or if you would use a database or other form of persistence and to explain your choice. How would you reply?
-I would not store chats in JSON files, a database because they're easier to index and have stronger transactions and migrations than JSON

4. What did you notice about performance using this file storage method?
-I did not really notice anything it felt the same, the storage method we used for the assignment are good this.

## Lab 2: MongoDB Integration & Performance Analysis

## QA
# Question 1: Performance Analysis
When I ran performance.py it wouldn't work so I was unable to see if I had completed the assignment correctly or not. However what I expected to see was flat_file_manager.py to slow down as the number of messages grew and MongoDB's speeds to stay relatively constant.

# Question 2: Atomic Operations
An atomic operation is an all or nothing database operation. It guarantees that the operation either completes fully or it fails completely, leaving the database in the state it was in before the operation began. There is no partial success state. The $-operators in MongoDB are atomic at the document level. This is important for a chat app due to multiple things happening at once, also known as concurrency.

# Question 3: Scalability
Finding all threads for a user: 

**FlatFileManager:** This is a complete disaster. To find all 10 threads for one user, you would have to scan all 10 million files, open each one, parse its JSON, and check the user_id field. This is unethically slow.

**MongoDBManager:** This is extremely fast. We created an index on the user_id field. MongoDB uses this index to instantly find the 10 documents associated with that specific user_id without scanning the rest.

Loading a specific conversation: 

**FlatFileManager:** If you already know the exact filename then this is fast. You just open one file.

**MongoDBManager:** This is also fast. Querying for this specific combination (user_id and thread_name) is one of the fastest operations MongoDB can do.

Storage organization: 

**FlatFileManager:** This would be unmanageable. Backing up, moving, or even just listing the files would be a nightmare.

**MongoDBManager:** This is exactly what MongoDB is built for. It manages its own data files internally, pre-allocating space and handling collections with billions of documents.

# Question 4: Data Modeling Design Challenge
Advantage of Embedded Messages Design: Read efficiency. You can retrieve an entire conversation, including all its messages, in a single database read operation. This is perfect for loading chat history. It's simple and fast.

Advantage of Separate Message Documents Design: Flexibility and scalability. A single conversation could eventually be infinite because each message is a separate document that would grow and grow. It's also easier to query across all messages.

# Scenarios: 
You would want to use the Separate Message Design if you had a feature like global message search (like Discord's keyword search). It's also a good choice if any single conversation is expected to be extremely long.
