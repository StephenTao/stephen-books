## Portal Partial Save Introduction

##### Document Control - Amendment History
|Version Number|Description of Change| Person Making Change | Date      |
| :-------------: | :-------------------| :----------------: | --------- |
| 1.0      | initialization      |  Stephen Huang   | 3/20/2018 |
            
### 3.  Examples

#### Example 1: Common function `updateRiskAnalysis`

* Example 1-backend 
  * Add updateRiskAnalysis in PartialSaveHandler
  * The method handler need extends from PartialSaveBaseHandler, in  PartialSaveBaseHandler have some common methods.
```java
package com.mig.edge.capabilities.quote.lob.common.handler

...

class PartialSaveHandler extends PartialSaveBaseHandler {
  ...
  
  @JsonRpcRunAsInternalGWUser
  @JsonRpcMethod
  public function updateRiskAnalysis(quoteID: String, draftDataDto: DraftDataDTO): DraftDataDTO {
    var job = getJob(quoteID)
    var policyPeriod = job.ResultingBoundPeriod == null ? QuoteUtil.getBasePeriod(job) : job.ResultingBoundPeriod

    var result = returnResultWithValidation(policyPeriod, draftDataDto, \period -> {
      gw.transaction.Transaction.runWithNewBundle(\ bundle -> {
        init_update(bundle,period,draftDataDto)
        _riskAnalysisPlugin.updateRiskAnalysis(period,draftDataDto.RiskAnalysis) //update RiskAnalysis data logic
      })
      draftDataDto.RiskAnalysis = _riskAnalysisPlugin.toDTO(period) //Only to DTO after updated RiskAnalysis
      // Some DraftDataDTO need updeted
      draftDataDto.AvaliableContactAddresses = _policyInfoPlugin.getAvaliableContactAddresses(period)
      draftDataDto.PeriodStatus = period.Status
      return draftDataDto
    })
    return result
  }
  
  ...
}
```
