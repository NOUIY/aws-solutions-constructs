# aws-wafwebacl-apigateway module
<!--BEGIN STABILITY BANNER-->

---

![Stability: Experimental](https://img.shields.io/badge/stability-Experimental-important.svg?style=for-the-badge)

> All classes are under active development and subject to non-backward compatible changes or removal in any
> future version. These are not subject to the [Semantic Versioning](https://semver.org/) model.
> This means that while you may use them, you may need to update your source code when upgrading to a newer version of this package.

---
<!--END STABILITY BANNER-->

| **Reference Documentation**:| <span style="font-weight: normal">https://docs.aws.amazon.com/solutions/latest/constructs/</span>|
|:-------------|:-------------|
<div style="height:8px"></div>


| **Language**     | **Package**        |
|:-------------|-----------------|
|![Python Logo](https://docs.aws.amazon.com/cdk/api/latest/img/python32.png) Python|`aws_solutions_constructs.aws_wafwebacl_apigateway`|
|![Typescript Logo](https://docs.aws.amazon.com/cdk/api/latest/img/typescript32.png) Typescript|`@aws-solutions-constructs/aws-wafwebacl-apigateway`|
|![Java Logo](https://docs.aws.amazon.com/cdk/api/latest/img/java32.png) Java|`software.amazon.awsconstructs.services.wafwebaclapigateway`|

## Overview
This AWS Solutions Construct implements an AWS WAF connected to Amazon API Gateway REST API.

Here is a minimal deployable pattern definition in Typescript:

``` typescript
import * as api from '@aws-cdk/aws-apigateway';
import * as lambda from "@aws-cdk/aws-lambda";
import { WafWebACLToApiGatewayProps, WafWebACLToApiGateway } from "@aws-solutions-constructs/aws-wafwebacl-apigateway";

const lambdaProps: lambda.FunctionProps = {
    code: lambda.Code.fromAsset(`${__dirname}/lambda`),
    runtime: lambda.Runtime.NODEJS_12_X,
    handler: 'index.handler'
};

const lambdafunction = new lambda.Function(this, 'LambdaFunction', lambdaProps);
const apiGateway = new api.RestApi(this, 'test-api');
const tasks = apiGateway.root.addResource('tasks');

apiGateway.addMethod('GET', new api.LambdaIntegration(lambdaFunction));

const apigatewayInterface = api.RestApi.fromRestApi(this, 'apigateway-interface', apiGateway.restApiId);

new WafWebACLToApiGateway(this, 'test-wafwebacl-apigateway', {
    existingApiGateway: apigatewayInterface
});

```

## Initializer

``` text
new WafWebACLToApiGateway(scope: Construct, id: string, props: WafWebACLToApiGatewayProps);
```

_Parameters_

* scope [`Construct`](https://docs.aws.amazon.com/cdk/api/latest/docs/@aws-cdk_core.Construct.html)
* id `string`
* props [`WafWebACLToApiGatewayProps`](#pattern-construct-props)

## Pattern Construct Props

| **Name**     | **Type**        | **Description** |
|:-------------|:----------------|-----------------|
|existingApiGateway|[`api.IRestApi`](https://docs.aws.amazon.com/cdk/api/latest/docs/@aws-cdk_aws-apigateway.IRestApi.html)|The existing API Gateway instance that will be fronted with the WAF.|
|webACLProps?|[`waf.CfnWebACLProps`](https://docs.aws.amazon.com/cdk/api/latest/docs/@aws-cdk_aws-waf.CfnWebACLProps.html)|Optional user-provided props to override the default props for the AWS WAF access control list.|

## Pattern Properties

| **Name**     | **Type**        | **Description** |
|:-------------|:----------------|-----------------|
|webACL|[`waf.CfnWebACL`](https://docs.aws.amazon.com/cdk/api/latest/docs/@aws-cdk_aws-waf.CfnWebACL.html)|Returns an instance of the waf.CfnWebACL created by the construct.|
|apiGateway|[`api.RestApi`](https://docs.aws.amazon.com/cdk/api/latest/docs/@aws-cdk_aws-apigateway.RestApi.html)|	Returns an instance of the API Gateway REST API created by the pattern. |

## Default settings

Out of the box implementation of the Construct without any override will set the following defaults:

### AWS WAF
* Deploy a WAF with 7 [AWS managed rule groups](https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-list.html)
    * AWSManagedRulesBotControlRuleSet
    * AWSManagedRulesKnownBadInputsRuleSet
    * AWSManagedRulesCommonRuleSet
    * AWSManagedRulesAnonymousIpList
    * AWSManagedRulesAmazonIpReputationList
    * AWSManagedRulesAdminProtectionRuleSet
    * AWSManagedRulesSQLiRuleSet
* Send metrics to Amazon CloudWatch

### Amazon API Gateway
* User provided API Gateway object is used as-is

## Architecture
![Architecture Diagram](architecture.png)

***
&copy; Copyright 2021 Amazon.com, Inc. or its affiliates. All Rights Reserved.