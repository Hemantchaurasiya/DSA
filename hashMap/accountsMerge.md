# Accounts Merge

---

## Problem Overview

Given a list of accounts where each account has a name and a list of email addresses, the goal is to merge accounts that share at least one common email address. The output should be a list of accounts with merged emails, each sorted lexicographically, and with the account name at the start.

**Example:**
```
Input:
accounts = [
  ["John", "johnsmith@mail.com", "john00@mail.com"],
  ["John", "johnnybravo@mail.com"],
  ["John", "johnsmith@mail.com", "john_newyork@mail.com"],
  ["Mary", "mary@mail.com"]
]

Output:
[
  ["John", "john00@mail.com", "john_newyork@mail.com", "johnsmith@mail.com"],
  ["John", "johnnybravo@mail.com"],
  ["Mary", "mary@mail.com"]
]
```

---

## Approach 1: Brute Force (Direct Comparison)

### Core Idea
Compare each account with every other account to identify shared emails. Merge accounts when shared emails are found.

### Algorithm
1. For each account, compare with every other account.
2. Check for common emails by intersecting email lists.
3. If common emails exist, merge the two accounts:
   - Combine email lists.
   - Keep track of merged accounts to avoid duplicates.
4. After all merges, for each merged group:
   - Remove duplicate emails.
   - Sort emails lexicographically.
   - Attach the account name.

**Note:** This approach is inefficient for large datasets due to repeated comparisons.

### Java Code
```java
class Solution {
    public List<List<String>> accountsMerge(List<List<String>> accounts) {
        int n = accounts.size();
        boolean[] visited = new boolean[n];
        List<List<String>> result = new ArrayList<>();

        for (int i = 0; i < n; i++) {
            if (visited[i]) continue;
            Set<String> emails = new HashSet<>(accounts.get(i).subList(1, accounts.get(i).size()));
            String name = accounts.get(i).get(0);
            for (int j = i + 1; j < n; j++) {
                if (visited[j]) continue;
                List<String> account = accounts.get(j);
                Set<String> compareEmails = new HashSet<>(account.subList(1, account.size()));
                // Check for intersection
                Set<String> intersection = new HashSet<>(emails);
                intersection.retainAll(compareEmails);
                if (!intersection.isEmpty()) {
                    // Merge emails
                    emails.addAll(compareEmails);
                    visited[j] = true;
                }
            }
            List<String> mergedAccount = new ArrayList<>();
            mergedAccount.add(name);
            List<String> emailList = new ArrayList<>(emails);
            Collections.sort(emailList);
            mergedAccount.addAll(emailList);
            result.add(mergedAccount);
        }
        return result;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  - Comparing every pair: `O(N^2)` where `N` is the number of accounts.
  - Intersection and merging emails: `O(M)` per comparison, where `M` is the total number of emails.
  - Overall: **O(N^2 * M)**, which is inefficient for large datasets.
- **Space Complexity:**  
  - Storing emails and merged results: `O(N * M)`.

---

## Approach 2: Graph + DFS / Union-Find (Optimal Solution)

### Core Idea
Model emails as nodes in a graph. Connect emails that belong to the same account. Use Union-Find or DFS to find connected components, where each component represents merged emails belonging to the same user.

### Algorithm
**Using Union-Find:**
1. Build a Union-Find data structure:
   - Each email is a node.
   - For each account, union all emails in that account.
2. After union operations, find the root parent for each email.
3. Group emails by their root parent.
4. For each group:
   - Sort emails lexicographically.
   - Retrieve the associated account name from the original data.
   - Output the merged account.

### Java Code
```java
class Solution {
    Map<String, String> emailToName = new HashMap<>();
    Map<String, String> parent = new HashMap<>();
    Map<String, List<String>> unions = new HashMap<>();

    public List<List<String>> accountsMerge(List<List<String>> accounts) {
        // Initialize Union-Find
        for (List<String> account : accounts) {
            String name = account.get(0);
            for (int i = 1; i < account.size(); i++) {
                String email = account.get(i);
                emailToName.put(email, name);
                parent.putIfAbsent(email, email);
            }
        }
        // Union emails belonging to the same account
        for (List<String> account : accounts) {
            String firstEmail = account.get(1);
            for (int i = 2; i < account.size(); i++) {
                union(account.get(i), firstEmail);
            }
        }
        // Group emails by root parent
        for (String email : parent.keySet()) {
            String root = find(email);
            unions.computeIfAbsent(root, x -> new ArrayList<>()).add(email);
        }
        // Prepare output
        List<List<String>> result = new ArrayList<>();
        for (String root : unions.keySet()) {
            List<String> emails = unions.get(root);
            Collections.sort(emails);
            String name = emailToName.get(root);
            List<String> accountInfo = new ArrayList<>();
            accountInfo.add(name);
            accountInfo.addAll(emails);
            result.add(accountInfo);
        }
        return result;
    }

    private String find(String email) {
        if (!parent.get(email).equals(email)) {
            parent.put(email, find(parent.get(email))); // Path compression
        }
        return parent.get(email);
    }

    private void union(String email1, String email2) {
        String root1 = find(email1);
        String root2 = find(email2);
        if (!root1.equals(root2)) {
            parent.put(root1, root2);
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  - Building the union-find structure: `O(N * M)`, where `N` is number of accounts and `M` total emails.
  - Union-find operations (with path compression): approximately `O(α(N * M))`, almost constant.
  - Grouping and sorting emails: `O(N * M log M)` in worst case.
  - Overall: **O(N * M log M)**.
- **Space Complexity:**  
  - Storing email-to-name map, parent map, and unions: `O(N * M)`.

---

## Summary Table

| Approach | Core Idea | Algorithm Highlights | Code Snippet | Time Complexity | Space Complexity |
|------------|------------|------------------------|--------------|-----------------|------------------|
| Brute Force | Pairwise comparison | Compare all accounts for shared emails | Provided above | `O(N^2 * M)` | `O(N * M)` |
| Graph + DFS | Build email graph, find connected components via DFS | Union emails in same account, DFS to find components | Not provided here | `O(N * M log M)` | `O(N * M)` |

---

## Final Tips for Interview Preparation
- Understand the **Union-Find (Disjoint Set Union)** data structure thoroughly, especially with path compression.
- Practice handling large datasets efficiently.
- Be comfortable with graph modeling problems involving connected components.
- Always consider edge cases such as accounts with no shared emails and duplicate emails.

---

This comprehensive guide should help you quickly grasp different approaches for **Accounts Merge** and prepare effectively for interviews.
