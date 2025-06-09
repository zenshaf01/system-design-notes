# Functional requirements
- User should be able to upload and view photos and videos
- User can follow other users
- The system should generate and display a users news feed which consists of top posts from the people the user follows

# Non Functional
- System should be highly available
- Should not have data loss

# High level design

Client -> App server
App Server -> Metadata store
App Server -> Block Storage
App Server -> Feed Generation System


# Data schema - 
- Users
    - Id
    - Name
    - Password
    - Creation Date
    - Last Login
- User Follow - Many to Many
    - Follower Id
    - Followee Id
- Post
    - Id
    - User Id
    - Photo URL
    - Photo caption / text
    - Likes
    - Photo Longitude
    - Photo Latitude
    - Creation Date

Database used: You can use an RDMS or a NoSQL. But Since there might be more writes for a post, the writes might get slower over time. You can have 

# Photo storage
- Object storage / Blob storage

# Feed Generation service
- Add new posts from all follower based on the recommendfation of the feed generation system

