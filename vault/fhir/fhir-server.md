- [Most FHIR Servers are Unusable in Production](https://vneilley.medium.com/most-fhir-servers-are-unusable-in-production-8833cb1480b1)
- [Amazon HealthLake — Does Generally Available mean Enterprise Ready?](https://vneilley.medium.com/amazon-healthlake-does-generally-available-mean-enterprise-ready-e815983a8a5)https://vneilley.medium.com/amazon-healthlake-does-generally-available-mean-enterprise-ready-e815983a8a5
- [zotero group: openehr](https://www.zotero.org/groups/11839)
- public servers
  - hapi: don't support compartment
  - firely
- get self link from response
    ```bash
    cat careplan.json  | jq '.link[] | select(.relation=="self") | .url'
    ```
