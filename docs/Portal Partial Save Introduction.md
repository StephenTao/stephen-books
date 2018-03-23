# Portal Partial Save Introduction

#### Document Control - Amendment History
|Version Number|Description of Change| Person Making Change | Date      |
| :-------------: | :-------------------| :----------------: | --------- |
| 1.0      | initialization      |  Stephen Huang   | 3/20/2018 |
            
## 2. Partial save common logic handle      

### 2.1 Backend code
```java

```

### 2.2 Frontend code
`C:\MIGPortalDevelopment\EdgeGatewayPortal\app\customer\js\src\edge\quoteandbind\common\controllers\WizardFlowCtrl.js`

#### 2.2.1 Function partialSaveSubmissionWithValidation()
Function Parameters:
* `saveFunction`: Required, do update function
* `afterSaveProcess`: Optional, do saveFunction success will do afterSaveProcess function, handle response data.
* `hideProgressMsg`: Optional, flag if show mask modal
```javascript
ctrl.partialSaveSubmissionWithValidation = function (saveFunction,afterSaveProcess,hideProgressMsg) {
    var deferred = $q.defer();
    var savePromise = deferred.promise;
    var currentStepName = $state.current.name;
    if($scope.quoteandbind.submission.draftData.isInPackage === true){
        WizardService.setPolicyTypeByParentStep($state.current,$scope.quoteandbind.submission.draftData);
    }
    var highWatermarkStepName = $scope.quoteandbind.submission.draftData.highWaterMarkScreen;
    $scope.quoteandbind.submission.draftData.currentScreen = $state.current.name;
    if(!highWatermarkStepName || WizardService.isCurrentStateAfterState(currentStepName, highWatermarkStepName)) {
        $scope.quoteandbind.submission.draftData.highWaterMarkScreen = currentStepName;
    }
    ctrl.setTargetScreenForDraftData();
    //1. Modal progress is show control
    if(!hideProgressMsg){
        ModalService.showProgressDialog($filter('translate')(JobTypeService.text.savingMessage), savePromise);
    }
    //2. Do save function
    savePromise = saveFunction();
    savePromise.then(function (partialDraftData) {
        DirtyFlagFactory.recordEndTime();
        $scope.quoteandbind.submission.draftData.periodStatus = partialDraftData.periodStatus;
        //3. Do call back function after save success.
        afterSaveProcess(partialDraftData);
        deferred.resolve();
    }, function (error, status, headers) {
        return deferred.reject([error, status, headers]);
    });
    return deferred.promise;    
};
```

#### 2.2.2 Function defaultPartailSave()
Function Parameters:
* `func`: Required, do partail update function
* `callback`: Optional, handle response validation result or special logic.
* `hideProgressMsg`: Optional, flag if show mask modal

This method is a further encapsulation of the `partialSaveSubmissionWithValidation` method. This method has synchronized to the front-end data with the updated response data. When we use `defaultPartailSave` we only need to care about the implementation of the update method and additional logic processing after the update is successful.

```javascript
ctrl.defaultPartailSave = function(func,callback,hideProgressMsg){
    var params = {
        quoteID: $scope.quoteandbind.submission.quoteID,
        sessionUUID: $scope.quoteandbind.submission.sessionUUID,
        draftDataDTO: $scope.quoteandbind.submission.draftData
    };
    if(hideProgressMsg == null){
        hideProgressMsg = false;
    }
    var savePromise = $scope.partialSaveSubmissionWithValidation(
        _.partial(func,'quoteandbind',params),
        _.partial(function (partialDraftData) {
            ...//ignore code sync updated data to forntend
            if((callback != null && typeof callback === 'function')){
                callback(hideProgressMsg);
            }
         }, _),
         hideProgressMsg
    );
    return savePromise;
};
```

## 3. Examples

### Example 0 : How to use? Your code structure is as follows.
* Backend code structure
```java
package com.*.handler
... //ignore code

class SomeHandler extends PartialSaveBaseHandler {
  ... //ignore code
  
  @JsonRpcRunAsInternalGWUser
  @JsonRpcMethod
  public function updateSomePart(quoteID: String, draftDataDto: DraftDataDTO) : DraftDataDTO {
     //1. Get policyPeriod 
     var job = getJob(quoteID)
     var policyPeriod = job.ResultingBoundPeriod == null ? QuoteUtil.getBasePeriod(job) : job.ResultingBoundPeriod
     
     var result = returnResultWithValidation(policyPeriod, draftDataDto, \period -> {
        gw.transaction.Transaction.runWithNewBundle(\ bundle -> {
        init_update(bundle,period,draftDataDto)
        //2. update some part data logic
        doSomePartUpdateFn()
      })
      //3. Reacquire SomePart DTO data
      draftDataDto.SomePart = getSomePartDTOFn()
      //4. Reacquire some DraftDataDTO need updeted, such as PeriodStatus. 
      draftDataDto.PeriodStatus = period.Status//this filed when submission Quoted back to update SomePart will change to Draft.
      
      return draftDataDto
    })
    return result
  }
  
  ... //ignore code
} 
```
* Frontend code structure
```javascript
function partialSave(){
    //1. get the params, hideProgressMsg is not required
    var hideProgressMsg, quoteID, draftDataDto;
    //2. do save customer function
    var saveFunction = function () {
        return XXXService.updateSomePart(quoteID, draftDataDto);
    };
    //3. save success call back function, do customer logic
    var afterSaveProcess = function(partialSavedData) {
        //update current modelAndView and handel validation result.
    }
    //4. Use common partail save method in WizadFlowCtl.js
    $scope.partialSaveSubmissionWithValidation(saveFunction, afterSaveProcess, hideProgressMsg);
}
```


### Example 1 : Common function `updateRiskAnalysis`

* Example 1-backend 
`C:\MIGGWProject\Workspace\r1\PolicyCenter\modules\configuration\gsrc\com\mig\edge\capabilities\quote\lob\common\handler\PartialSaveHandler.gs`
  * Add updateRiskAnalysis in PartialSaveHandler
  * The method handler need extends from PartialSaveBaseHandler, in  PartialSaveBaseHandler have some common methods.
```java
package com.mig.edge.capabilities.quote.lob.common.handler

... //ignore code

class PartialSaveHandler extends PartialSaveBaseHandler {
  ... //ignore code
  
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
  
  ... //ignore code
}
```

* Example 1-Frontend 
`C:\MIGPortalDevelopment\EdgeGatewayPortal\app\customer\js\src\edge\quoteandbind\common\controllers\WireFrameRiskAnalysisCtrl.js`

```javascript
... //ignore code
function partialSave(){
    //1. Params
    var params = {
        quoteID: $scope.quoteandbind.submission.quoteID,
        draftDataDTO: $scope.quoteandbind.submission.draftData
    };
    //2. Use common partail save method in WizadFlowCtl.js
    $scope.partialSaveSubmissionWithValidation(
        _.partial(CommonCustomService.updateRiskAnalysis,'quoteandbind',params),
        _.partial(function (partialSavedData) {
            $scope.quoteandbind.submission.draftData.riskAnalysis = partialSavedData.riskAnalysis;
            setLineValidationResult(); // handle validation result
        }, _)
    );
}
... //ignore code
```
