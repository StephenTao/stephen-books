## Portal Partial Save Introduction

### Document Control - Amendment History
| Version Number  | Description of Change | Person Making Change | Date      |
| :-------------- | ---------------------:| :------------------: | --------- |
| 1.0             | initialization        |  Stephen Huang       | 3/20/2018 |
            
### 3.  Examples

#### Example 1: Common function updateRiskAnalysis

* Example 1-backend 
  * Add updateRiskAnalysis in PartialSaveHandler
  * The method handler need extends from PartialSaveBaseHandler, in  PartialSaveBaseHandler have some common methods.
```java
class PartialSaveHandler extends PartialSaveBaseHandler
```
