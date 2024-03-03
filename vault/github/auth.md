- ways to authentication
  - ~/.netrc
  ```
  machine github.com
    login USERNAME
    password PASSWORD
  ```
   [!WARNING]
   Git uses a utility called cURL under the covers, which respects the use of the .netrc file. Be aware that other applications that use cURL to make requests to servers defined in your .netrc file will also now be authenticated using these credentials.
  - .netrc with gpg
  - .git/config
  - credential helper
  - store
  - cache
