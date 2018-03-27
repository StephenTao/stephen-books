# Portal Partial Save Introduction

#### Document Control - Amendment History
|Version Number|Description of Change| Person Making Change | Date      |
| :-------------: | :-------------------| :----------------: | --------- |
| 1.0      | initialization      |  Stephen Huang   | 3/20/2018 |
            
            
## 1. What is `Portal Partial Save`?

### 1.1 Why do this?
In oder to improve performance and user interface, partial save only update current screen data and refresh current screen data.
### 1.2 How to do this?
Backend using AOP programming ideas, add update curren screen function, add before and after update common function.
### 1.3 Some tips
* 
* 

## 2. Partial save common logic handle      

### 2.1 Backend code

#### 2.1.1 Super handler class PartialSaveBaseHandler
`C:\MIGGWProject\Workspace\r1\PolicyCenter\modules\configuration\gsrc\com\mig\edge\capabilities\quote\lob\common\handler\PartialSaveBaseHandler.gs` 

This is super parital save handler class. It was already implemented basic function such as common data update and update validation handle. 

Basic common function: 
* `init_update` : before update, do some common logic update. such as `setPolicyPeriodToEditable`
* `returnResultWithValidation` : after updated, do common logic handle response, such as handle exception
    * `period`   --                 current policy period
    * `originalDraftData` --         before update DTO
    * `cb(period : PolicyPeriod)`  -- call back function
```java
package com.mig.edge.capabilities.quote.lob.common.handler

class PartialSaveBaseHandler implements IRpcHandler {

  protected function init_update(bundle: Bundle, policyPeriod: PolicyPeriod, draftDataDto: DraftDataDTO) {
    PortalSessionService_Ext.setTargetScreen(draftDataDto.TargetScreen)
    policyPeriod=bundle.add(policyPeriod)
    setPolicyPeriodToEditable(policyPeriod) // update current policyPeriod editable
    /*method canEdit will return false for submission status' quoted & approved*/
    /*periodStatusChangeAfterQuote(policyPeriod)*/
    policyPeriod.Job.PortalCurrentScreen_Ext = draftDataDto.CurrentScreen
    policyPeriod.Job.PortalHighWaterMarkScreen_Ext = draftDataDto.HighWaterMarkScreen
    //set current line under CPP, must prior to super.updateSubmission
    if (policyPeriod.IsInPackage) {
      policyPeriod.Job.PortalCurrentLineUnderCPP_Ext = draftDataDto.CurrentLineUnderCPP
    }
    policyPeriod.Job.IsPartialPersistentSave_Ext = false
  }
  
  // handle exception 
  public function returnResultWithValidation<T>(period : PolicyPeriod, originalDraftData: DraftDataDTO, cb(period : PolicyPeriod):T): DraftDataDTO{
    var resultWithValidationMsg: DraftDataDTO
    try {
      originalDraftData.IsPartialPersistentSave = period.Job.IsPartialPersistentSave_Ext
      resultWithValidationMsg = cb(period) as DraftDataDTO // do call back function
      resultWithValidationMsg.IsPartialPersistentSave = period.Job.IsPartialPersistentSave_Ext
    } catch (ex: PortalScreenValidationException) {
      return handlerValidationResultsLineSpecific(originalDraftData, ex.lineValidationResultDTO, originalDraftData, ex.productCode)
    } catch(ex: java.lang.Exception){
      var lvDto = new LineValidationResultDTO_Ext()
      lvDto.HasErrors = true
      lvDto.ErrorMessages = {"Quote save error: "+ex.toString()}
      return handlerValidationResultsLineSpecific(originalDraftData, lvDto, originalDraftData, originalDraftData.ProductCode)
    }
    return handlerValidationResultsLineSpecific(resultWithValidationMsg, null, resultWithValidationMsg, resultWithValidationMsg.ProductCode)
  }
  
  // set validation messages to related LOB
  public function handlerValidationResultsLineSpecific(returnQdd: DraftDataDTO, validationResult: LineValidationResultDTO_Ext, incomingQdd: DraftDataDTO, productCode: String): DraftDataDTO {
     //ingrone code
  }
}
```

#### 2.1.2 Usage in partial Handler

Code structure:
* Partial save handler need extend `PartialSaveBaseHandler`
* Using super class function get job entity
* Using super function `returnResultWithValidation` and `init_update` like this.
* After updated, we sholud synchronized response data and refresh DTO data.

```java
package com.*.handler
... //ignore code

class xxxHandler extends PartialSaveBaseHandler {
  ... //ignore code
  
  @JsonRpcRunAsInternalGWUser
  @JsonRpcMethod
  public function updateFunction(quoteID: String, draftDataDto: DraftDataDTO) : DraftDataDTO {
     //1. Get policyPeriod 
     var job = getJob(quoteID)
     var policyPeriod = job.ResultingBoundPeriod == null ? QuoteUtil.getBasePeriod(job) : job.ResultingBoundPeriod
     
     var result = returnResultWithValidation(policyPeriod, draftDataDto, \period -> {
        gw.transaction.Transaction.runWithNewBundle(\ bundle -> {
            init_update(bundle,period,draftDataDto)
            //2. do update some part data logic
            doSpecificUpdateLogic()
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

This method is a further encapsulation of the `partialSaveSubmissionWithValidation` method. This method has synchronized to the front-end data with the updated response data. When we use `defaultPartailSave` we only need to care about the implementation of the update method and additional logic processing after the update is successful. So we recommend using `defaultPartailSave`.

this function have a issue about parameter `callback` function do not pass updated response data, so the call back function can't sync backend and frontend data.


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

#### 2.2.3 Frontend code structure
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

## 3. Examples

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

Why do not use `defaultPartailSave` method ?
`defaultPartailSave` do not synchronized '$scope.quoteandbind.submission.draftData.riskAnalysis', so we use `partialSaveSubmissionWithValidation`, the method parameters of `afterSaveProcess` function can customize synchronized data.
    
```java
//if use deafultPartialSave like this
function defaultPartailSave() {
    $scope.defaultPartailSave(
        CommonCustomService.updateRiskAnalysis, 
        setLineValidationResult()
    );
}
```
