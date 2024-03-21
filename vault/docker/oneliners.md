- get ip address based on container name 
  ```bash
   docker container inspect $(docker container ls --filter=name=redis- -q) \
       --format '{{json .NetworkSettings.Networks.bridge.IPAddress}}'
  ```  
