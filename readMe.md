# Tokens - Auth

```plaintext
+--------------------+              +-------------------+
|    Refresh Token   |  -------->  |    Access Token    |
| Long-lived Token   |              | Short-lived Token |
| Used to Request    |              | Used for API      |
| New Access Tokens  |              | Authentication    |
+--------------------+              +-------------------+
         |
         v
+-------------------------------------+
|         Authentication Server       |
| Issues new Access Token when valid  |
+-------------------------------------+
