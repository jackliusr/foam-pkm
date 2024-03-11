- Prefer headers
  - respond-async
    ```http
     POST /collection HTTP/1.1
     Host: example.org
     Content-Type: text/plain
     Prefer: respond-async
    ```
  - return=representation and return=minimal
    ```http
     PATCH /item/123 HTTP/1.1
     Host: example.org
     Content-Type: application/example-patch
     Prefer: return=representation
    ```
  - wait
    ```http
     POST /collection HTTP/1.1
     Host: example.org
     Content-Type: text/plain
     Prefer: respond-async, wait=10   
    ```
  - handling=strict and handling=lenient
    ```http
     POST /collection HTTP/1.1
     Host: example.org
     Content-Type: text/plain
     Prefer: handling=strict
    ```
