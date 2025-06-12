# Design Twitter

This guide covers the problem of designing a simplified version of Twitter, which involves posting tweets, following/unfollowing users, and retrieving a news feed. We will examine multiple approaches, from naive to the most efficient, with explanations, code snippets, and dry runs.

---

## 1. Brute Force Approach

### **Core Idea:**
Maintain a list of tweets for each user. To get a user's news feed, scan **all tweets** from all users to find latest tweets from users that the current user follows.

### **Algorithm:**
- Use a global list of all tweets, each with userId and timestamp.
- For each user, maintain a set of followees.
- **PostTweet:** Append new tweet with timestamp.
- **GetNewsFeed:** Iterate over all tweets in reverse order (latest first), collect tweets from followees and self until 10 tweets are collected.
- **Follow/Unfollow:** Update the followee set.

### **Java Code:**

```java
import java.util.*;

class Twitter {
    private static class Tweet {
        int tweetId, userId, timestamp;
        Tweet(int tweetId, int userId, int timestamp) {
            this.tweetId = tweetId;
            this.userId = userId;
            this.timestamp = timestamp;
        }
    }

    private List<Tweet> allTweets = new ArrayList<>();
    private Map<Integer, Set<Integer>> followeesMap = new HashMap<>();
    private int timestamp = 0;

    public Twitter() {}

    public void postTweet(int userId, int tweetId) {
        allTweets.add(new Tweet(tweetId, userId, timestamp++));
    }

    public List<Integer> getNewsFeed(int userId) {
        Set<Integer> followees = followeesMap.getOrDefault(userId, new HashSet<>());
        followees.add(userId); // include self

        List<Integer> feed = new ArrayList<>();
        for (int i = allTweets.size() - 1; i >= 0 && feed.size() < 10; i--) {
            Tweet tweet = allTweets.get(i);
            if (followees.contains(tweet.userId))
                feed.add(tweet.tweetId);
        }
        return feed;
    }

    public void follow(int followerId, int followeeId) {
        followeesMap.computeIfAbsent(followerId, k -> new HashSet<>()).add(followeeId);
    }

    public void unfollow(int followerId, int followeeId) {
        if (followeesMap.containsKey(followerId))
            followeesMap.get(followerId).remove(followeeId);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - `postTweet`: O(1)  
  - `getNewsFeed`: O(N) where N is total number of tweets (scanning the entire list) – inefficient for large data.
  - `follow/unfollow`: O(1)
- **Space Complexity:** O(N), storing all tweets, follow relationships.

### **Dry Run:**
Suppose:
- User 1 posts tweets 5, 10.
- User 2 follows User 1.
- User 2 retrieves news feed.

Steps:
1. Post tweets with timestamps.
2. User 2's follow set includes User 1.
3. Retrieving news feed: scan all tweets backwards, pick latest from User 1 until 10 tweets.

---

## 2. Better Solution: Use Min-Heap & Priority Queue

### **Core Idea:**
Maintain a global list of tweets and, for each user, only track their followees. When fetching the news feed, use a **priority queue (max-heap)** to efficiently get the latest tweets from relevant users.

### **Algorithm:**
- Store tweets per user in a map: userId → list of tweets.
- When `getNewsFeed` is called:
  - Collect the last K tweets from each followee.
  - Use a max-heap to merge these tweets based on timestamp.
  - Extract top 10 tweets.

### **Java Code:**

```java
import java.util.*;

class Twitter {
    private static class Tweet {
        int tweetId, timestamp;
        Tweet(int tweetId, int timestamp) {
            this.tweetId = tweetId;
            this.timestamp = timestamp;
        }
    }

    private Map<Integer, List<Tweet>> userTweets = new HashMap<>();
    private Map<Integer, Set<Integer>> followeesMap = new HashMap<>();
    private int timestamp = 0;

    public Twitter() {}

    public void postTweet(int userId, int tweetId) {
        userTweets.computeIfAbsent(userId, k -> new ArrayList<>())
                  .add(new Tweet(tweetId, timestamp++));
    }

    public List<Integer> getNewsFeed(int userId) {
        Set<Integer> followees = followeesMap.getOrDefault(userId, new HashSet<>());
        followees.add(userId); // include self

        PriorityQueue<Tweet> maxHeap = new PriorityQueue<>((a, b) -> b.timestamp - a.timestamp);
        for (int user : followees) {
            List<Tweet> tweets = userTweets.getOrDefault(user, new ArrayList<>());
            for (int i = tweets.size() - 1; i >= Math.max(0, tweets.size() - 10); i--) {
                maxHeap.offer(tweets.get(i));
            }
        }

        List<Integer> newsFeed = new ArrayList<>();
        while (!maxHeap.isEmpty() && newsFeed.size() < 10) {
            newsFeed.add(maxHeap.poll().tweetId);
        }
        return newsFeed;
    }

    public void follow(int followerId, int followeeId) {
        followeesMap.computeIfAbsent(followerId, k -> new HashSet<>()).add(followeeId);
    }

    public void unfollow(int followerId, int followeeId) {
        if (followeesMap.containsKey(followerId))
            followeesMap.get(followerId).remove(followeeId);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - `postTweet`: O(1)  
  - `getNewsFeed`: O(F * K * log(K)), where F is number of followees, K is number of tweets per user (up to 10 here).  
- **Space Complexity:** O(N), storing all tweets and follow relationships.

### **Dry Run:**
- User 1 posts tweets 5, 10.
- User 2 follows User 1.
- When `getNewsFeed(2)` is called:
  - Collect last 10 tweets from User 1.
  - Use heap to merge, get top 10 latest tweets efficiently.

---

## 3. Most Optimal Solution: Using **HashMaps + Priority Queues + Lazy Evaluation**

### **Core Idea:**
- Maintain for each user:
  - A linked list or stack of their tweets (latest on top).
  - Followee relationships.
- When fetching the news feed, perform a k-way merge using a min-heap or max-heap with iterators over each user's tweets.  
- Use lazy evaluation: only build data structures when needed; cache results if necessary.

### **Algorithm:**
- For each user, maintain:
  - A linked list or stack of their tweets.
- For `getNewsFeed`:
  - Use a max-heap to perform a k-way merge of the latest tweets from followees.
  - Extract the top 10 tweets.

### **Implementation Note:**
- This approach is similar to the previous but emphasizes lazy evaluation and efficient merging.
- For simplicity, the previous approach suffices as the most optimized in typical scenarios.

---

## **Summary Table**

| Approach | Data Structures | Time Complexity | Space Complexity | Notes |
|------------|---------------------|-------------------|--------------------|--------|
| Brute Force | Global list, follow sets | O(N) for news feed | O(N) | Inefficient for large data |
| Priority Queue Merge | Map of user → tweets, heap | O(F * K log K) | O(N) | Better and scalable |
| Most Optimal | HashMaps, heaps, lazy merge | O(K log F) | O(N) | Efficient in large-scale systems |

---

## Final Tips for Interviews:
- Use hash maps for fast lookups of followees and tweets.
- Use heaps (priority queues) for efficiently merging latest tweets.
- Avoid scanning all data for large datasets; focus on only relevant data.
- Think about lazy evaluation and caching if the system requires high efficiency.

---

This concludes the comprehensive revision for **Design Twitter**. Practice these approaches to understand the trade-offs and implementation details!
