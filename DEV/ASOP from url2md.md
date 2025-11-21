---
doc-id: "f0bf5cd9-35bb-49d8-9b86-f353cec306b2"
title: "Untitled Document"
description: "[![Actuarial Standards Board](https://www.actuarialstandardsboard.org/wp-content/themes/asb/images/logo-transparent-600.png)](https://www.actuarialsta..."
created-date: "2025-11-21T00:01:27.607Z"
updated-date: "2025-11-21T00:01:27.607Z"
source-url: "https://www.actuarialstandardsboard.org/asops/modeling-3/"
purpose: "reference"
audience: ["developers","designers"]
tags: ["reference"]
reading-time-minutes: 25
images-list:
  - "https://www.actuarialstandardsboard.org/wp-content/themes/asb/images/logo-transparent-600.png"
  - "https://cdn.printfriendly.com/buttons/printfriendly-button-nobg.png"
  - "https://www.actuarialstandardsboard.org/wp-content/themes/asb/images/LinkedInIcon.png"
  - "https://www.actuarialstandardsboard.org/wp-content/themes/asb/images/twitterIcon.png"
  - "https://www.actuarialstandardsboard.org/wp-content/themes/asb/images/facebookIcon.png"
entities:
  - "Actuarial Standards Board"
  - "Capital Adequacy Assessment"
  - "Actuarial Opinion Not Based"
  - "Asset Adequacy Analysis"
  - "Life Insurance"
  - "Health Insurance Reserves"
  - "Related Actuarial Items"
  - "Download Here"
  - "Last Revised"
  - "Effective Date"
---
[![Actuarial Standards Board](https://www.actuarialstandardsboard.org/wp-content/themes/asb/images/logo-transparent-600.png)](https://www.actuarialstandardsboard.org "Home")

Modeling
========

[All ASOPs](/standards-of-practice/) » [Home](http://www.actuarialstandardsboard.org/) » Modeling

[](https://www.actuarialstandardsboard.org)

[Previous  
ASOP 55](https://www.actuarialstandardsboard.org/asops/capital-adequacy-assessment/ "Capital Adequacy Assessment")

[Next  
ASOP 57](https://www.actuarialstandardsboard.org/asops/asop-no-57-statements-of-actuarial-opinion-not-based-on-an-asset-adequacy-analysis-for-life-insurance-annuity-or-health-insurance-reserves-and-related-actuarial-items/ "Statements of Actuarial Opinion Not Based on an Asset Adequacy Analysis for Life Insurance, Annuity, or Health Insurance Reserves and Related Actuarial Items")

PDF Version: [Download Here](https://www.actuarialstandardsboard.org/wp-content/uploads/2020/01/asop056_195-1.pdf)  
Last Revised: December 2019  
Effective Date: October 01, 2020  
Document Number: 195  
Document Status: Adopted  

[General](https://www.actuarialstandardsboard.org/portfoliocategory/general/)

### Search Sitewide

 

### Search This Document

 search next  
  
  

**Warning:** Each web version of an ASOP is provided “as is” for convenience of the user only and is not, and should not be considered to be, the official version of an ASOP.  The American Academy of Actuaries does not warrant or represent that the web version of any ASOP is accurate and disclaims any and all warranties that are or might otherwise be applicable including, without limitation, any warranties of merchantability or fitness for a particular purpose.  The official version of an ASOP is as set forth in the PDF version of the ASOP, which may be downloaded from this site.

### Table of Contents

*   [TRANSMITTAL MEMORANDUM](#transmittal-memorandum)
*   [Section 1. Purpose, Scope, Cross References, and Effective Date](#section-1-purpose-scope-cross-references-and-effective-date)
    *   [1.1 Purpose](#11-purpose)
    *   [1.2 Scope](#12-scope)
    *   [1.3 Cross References](#13-cross-references)
    *   [1.4 Effective Date](#14-effective-date)
*   [Section 2. Definitions](#section-2-definitions)
    *   [2.1 Assumption](#21-assumption)
    *   [2.2 Data](#22-data)
    *   [2.3 Governance and Controls](#23-governance-and-controls)
    *   [2.4 Hold‐out Data](#24-holdout-data)
    *   [2.5 Input](#25-input)
    *   [2.6 Intended Purpose](#26-intended-purpose)
    *   [2.7 Intended User](#27-intended-user)
    *   [2.8 Model](#28-model)
    *   [2.9 Model Risk](#29-model-risk)
    *   [2.10 Model Run](#210-model-run)
    *   [2.11 Output](#211-output)
    *   [2.12 Overfitting](#212-overfitting)
    *   [2.13 Parameter](#213-parameter)
*   [Section 3. Analysis of Issues and Recommended Practices](#section-3-analysis-of-issues-and-recommended-practices)
    *   [3.1 Model Meeting the Intended Purpose](#31-model-meeting-the-intended-purpose)
        *   [3.1.1 Designing, Developing, or Modifying the Model](#311-designing-developing-or-modifying-the-model)
        *   [3.1.2 Selecting, Reviewing, or Evaluating the Model](#312-selecting-reviewing-or-evaluating-the-model)
        *   [3.1.3 Using the Model](#313-using-the-model)
        *   [3.1.4 Model Structure](#314-model-structure)
        *   [3.1.5 Data](#315-data)
        *   [3.1.6 Assumptions Used As Input](#316-assumptions-used-as-input)
    *   [3.2 Understanding the Model](#32-understanding-the-model)
    *   [3.3 Reliance on Data or Other Information Supplied by Others](#33-reliance-on-data-or-other-information-supplied-by-others)
    *   [3.4 Reliance on Models Developed by Others](#34-reliance-on-models-developed-by-others)
    *   [3.5 Reliance on Experts](#35-reliance-on-experts)
    *   [3.6 Evaluation and Mitigation of Model Risk](#36-evaluation-and-mitigation-of-model-risk)
        *   [3.6.1  Model Testing](#361-model-testing)
        *   [3.6.2 Model Output Validation](#362-model-output-validation)
        *   [3.6.3 Review by Another Professional](#363-review-by-another-professional)
        *   [3.6.4 Reasonable Governance and Controls](#364-reasonable-governance-and-controls)
        *   [3.6.5 Mitigating Misuse and Misinterpretation](#365-mitigating-misuse-and-misinterpretation)
    *   [3.7 Documentation](#37-documentation)
*   [Section 4. Communications and Disclosures](#section-4-communications-and-disclosures)
    *   [4.1 Required Disclosures in an Actuarial Report](#41-required-disclosures-in-an-actuarial-report)
    *   [4.2 Additional Disclosures in an Actuarial Report](#42-additional-disclosures-in-an-actuarial-report)
    *   [4.3 Confidential Information](#43-confidential-information)
*   [Appendix 1](#appendix-1)
    *   [Background and Current Practices](#background-and-current-practices)
*   [Appendix 2](#appendix-2)
    *   [Comments on the Fourth Exposure Draft and Responses](#comments-on-the-fourth-exposure-draft-and-responses)

**Actuarial Standard of Practice No. 56**
=========================================

**Modeling**

**STANDARD OF PRACTICE**

TRANSMITTAL MEMORANDUM
----------------------

**TO:** Members of Actuarial Organizations Governed by the Standards of Practice of the Actuarial Standards Board and Other Persons Interested in Modeling

**FROM:** Actuarial Standards Board (ASB)

**SUBJ:** Actuarial Standard of Practice (ASOP) No. 56

This document contains ASOP No. 56, _Modeling_.

History of the Standard

The ASB first began work on a standard for modeling in the late 1990s. Motivated primarily to address the role catastrophe modeling of earthquakes and hurricanes played in casualty ratemaking, this work was focused on the use of specialized models where actuaries would have to rely on a model that was developed by professionals other than actuaries. As a result of this work, ASOP No. 38, _Using Models Outside the Actuary’s Area of Expertise_, was approved by the ASB in June of 2000 with the scope of the standard limited to the Property/Casualty area of practice. Historically, ASOP No. 38 had been the only ASOP that specifically addressed modeling.

Recently, the number and importance of modeling applications in actuarial science have increased, with the results of actuarial models sometimes being reflected in financial statements.

Recognizing this trend, the ASB asked the Life Committee in 2010 to begin work on an ASOP focused on modeling. The Life Committee formed a task force to address this issue and, in February of 2012, a discussion draft titled _Modeling in Life Insurance and Annuities_ was released and nineteen comment letters were received. The transmittal letter also mentioned that the scope might be expanded to all practice areas and asked for comments on this idea.

Based upon the feedback received, and numerous other discussions on the topic of modeling, in December of 2012 the ASB created two multi-disciplinary task forces under the direction of the General Committee: i) a general Modeling Task Force, charged with developing an ASOP to address modeling applications in all practice areas, and ii) a Catastrophe Modeling Task Force to consider expanding ASOP No. 38 to all practice areas while focusing exclusively on using catastrophe models. The membership of these task forces has experience in all actuarial practice areas, including enterprise risk management.

First Exposure Draft

The first exposure draft was released in June 2013 with a comment deadline of September 30, 2013. Forty-eight comment letters were received and considered in making changes that were reflected in the second exposure draft.

Second Exposure Draft

A second exposure draft was released in November 2014 with a comment deadline of March 1, 2015. Thirty-seven comment letters were received and considered in making changes that were reflected in the third exposure draft.

Third Exposure Draft

A third exposure draft was released in June 2016 with a comment deadline of October 31, 2016. Twenty-eight comment letters were received and considered in making changes that were reflected in the fourth exposure draft.

Fourth Exposure Draft

A fourth exposure draft was released in December 2018 with a comment deadline of May 15, 2019. Twenty-six comment letters were received and considered in making changes that were reflected in this final ASOP. For a summary of the issues contained in these comment letters, please see appendix 2.

Notable Changes from the Fourth Exposure Draft

Notable changes made to the fourth exposure draft are summarized below. Additional changes were made to improve readability, clarity, or consistency.

1.  Section 3.1.6(b), Margins, was deleted because it did not provide sufficiently clear guidance. While margins are appropriately used, or even required, for certain intended purposes, margins are inappropriate and not used for other intended purposes.
2.  “Hold-out” data in predictive modeling was defined and added to the list of items that may be included in the model output validation in section 3.6.2(b).
3.  The term “parameter” was eliminated from section 3 of the ASOP, referencing it only within the definition of “assumption” because the two terms often are synonymous and the guidance often was identical.

As a next step, the ASB will review the previously approved but pending ASOP No. 38, _Catastrophe Modeling (for All Practice Areas)_, for any changes necessitated by this ASOP and take appropriate action.

The ASB thanks everyone who took the time to contribute comments and suggestions on the exposure drafts.

The ASB also thanks former task force member Aaron R. Weindling for his assistance during the earlier drafting of this standard.

The ASB voted in December 2019 to adopt this standard.

**Modeling Task Force**

Dale S. Hagstrom, Chairperson

Maryellen J. Coggins

Stephen Mildenhall

Julie H. Fried

Judy K. Stromback

Kenneth R. Kasner

 

**General Committee of the ASB**

Margaret Tiller Sherwood, Chairperson

Ralph S. Blanchard III

Susan E. Pantely

Andrew M. Erman

Judy K. Stromback

Dale S. Hagstrom

Hal Tepfer

Robert S. Miccolis

Christian J. Wolfe

**Actuarial Standards Board**

Kathleen A. Riley, Chairperson

Christopher S. Carlson

Darrell D. Knapp

Maryellen J. Coggins

Cande J. Olsen

Robert M. Damler

Barbara L. Snyder

Mita D. Drazilov

Patrick B. Woods

_The Actuarial Standards Board (ASB) sets standards for appropriate actuarial practice in the United States through the development and promulgation of Actuarial Standards of Practice (ASOPs). These ASOPs describe the procedures an actuary should follow when performing actuarial services and identify what the actuary should disclose when communicating the results of those services._

Section 1. Purpose, Scope, Cross References, and Effective Date
---------------------------------------------------------------

### 1.1 Purpose

This actuarial standard of practice (ASOP or standard) provides guidance to actuaries when performing actuarial services with respect to designing, developing, selecting, modifying, using, reviewing, or evaluating models.

### 1.2 Scope

This standard applies to actuaries in any practice area when performing actuarial services with respect to designing, developing, selecting, modifying, or using all types of models. For example, an actuary using a model developed by others in which the actuary is responsible for the model output is subject to this standard.

If the actuary’s actuarial services involve reviewing or evaluating models, the reviewing or evaluating actuary should be reasonably satisfied that the actuarial services were performed in accordance with this standard. The reviewing or evaluating actuary should apply the guidance in this standard to the extent practicable within the scope of the actuary’s assignment.

The guidance in this ASOP applies to the actuary when, in the actuary’s professional judgment, reliance by the intended user on the model output has a material effect for the intended user. This judgment should be made within the context of the use of the model output and the needs of the intended user, based on facts known by the actuary at the time the actuarial services are performed. For example, actuarial services performed in relation to pension plan contribution and cost projection models, insurance pricing models, predictive models, reserving models, and insurance company financial planning models may require application of the guidance in this ASOP. In assessing materiality, the actuary should be guided by ASOP No. 1, _Introductory Actuarial Standard of Practice_, section 2.6.

The guidance in this ASOP does not apply to the actuary when performing services with respect to individual pension benefit calculations and nondiscrimination testing, as described in section 1.2 of ASOP No. 4, _Measuring Pension Obligations and Determining Pension Plan Costs or Contributions_.

This standard only applies to the extent of the actuary’s responsibilities. The actuary’s responsibilities may extend to performing actuarial services related to an entire model or to only a small portion of a model.

Other ASOPs may provide guidance for actuarial services that involve models. If the actuary determines that the guidance from another ASOP conflicts with the guidance of this ASOP, the guidance of the other ASOP will govern.

If the actuary departs from the guidance set forth in this ASOP in order to comply with applicable law (statutes, regulations, and other legally binding authority), or for any other reason, the actuary should refer to section 4. If a conflict exists between this standard and applicable law, the actuary should comply with applicable law.

### 1.3 Cross References

When this ASOP refers to the provisions of other documents, the reference includes the referenced documents as they may be amended or restated in the future, and any successor to them, by whatever name called. If any amended or restated document differs materially from the originally referenced document, the actuary should consider the guidance in this ASOP to the extent it is applicable and appropriate.

### 1.4 Effective Date

This ASOP is effective for work performed on or after October 1, 2020.

Section 2. Definitions
----------------------

The terms below are defined for use in this actuarial standard of practice and appear in bold throughout the ASOP.

### 2.1 Assumption

A type of explicit input to a model that is derived from data, represents possibilities based on professional judgment, or may be prescribed by law or by others. When derived from data, an assumption may be statistical, financial, economic, mathematical, or scientific in nature, and may be described as a parameter.

### 2.2 Data

Facts or information that are either direct input to a model or inform the selection of input. Data may be collected from sources such as records, experience, experiments, surveys, observations, benefit plan or policy provisions, or output from other models.

### 2.3 Governance and Controls

The application of a set of procedures and an organizational structure designed to reduce the risk that the model output is not reliably calculated or not utilized as intended.

### 2.4 Hold‐out Data

A subset of data that is withheld intentionally when developing a predictive model so that the model may be validated later with data that were not used to develop the model.

### 2.5 Input

Data or assumptions used in a model to produce output.

### 2.6 Intended Purpose

The goal or question, whether generalized or specific, addressed by the model within the context of the assignment.

### 2.7 Intended User

Any person whom the actuary identifies as able to rely on the model output.

### 2.8 Model

A simplified representation of relationships among real world variables, entities, or events using statistical, financial, economic, mathematical, non-quantitative, or scientific concepts and equations. A model consists of three components: an information input component, which delivers data and assumptions to the model; a processing component, which transforms input into output; and a results component, which translates the output into useful business information.

### 2.9 Model Risk

The risk of adverse consequences resulting from reliance on a model that does not adequately represent that which is being modeled, or the risk of misuse or misinterpretation.

### 2.10 Model Run

The process of transforming a particular set of input to a particular set of output in a model. A model run could include the whole transformation process or part of the process, as applicable.

### 2.11 Output

The results of a model including, but not limited to, point estimates, likely or possible ranges, data or assumptions (as input for other models), behavioral expectations, or qualitative criteria on which decisions could be made.

### 2.12 Overfitting

A situation where a model fits the data used to develop the model so closely that prediction accuracy materially decreases when the model is applied to different data.

### 2.13 Parameter

A type of statistical, financial, economic, mathematical, or scientific value that is used as input to certain types of models. Examples of parameters include expected values in probability distributions and coefficients of formula variables. Some types of models, such as predictive or statistical models, produce estimates of parameters as output, which may be used as input to other models.

Section 3. Analysis of Issues and Recommended Practices
-------------------------------------------------------

### 3.1 Model Meeting the Intended Purpose

The actuary should understand the model’s intended purpose.

#### 3.1.1 Designing, Developing, or Modifying the Model

When the actuary designs, develops, or modifies the model, the actuary should confirm, in the actuary’s professional judgment, that the capability of the model is consistent with the intended purpose. Items the actuary should consider, if applicable, include but are not limited to the following:

a. the level of detail built into a model;

b. the dependencies recognized; and

c. the model’s ability to identify possible volatility of output, such as volatility around expected values.

#### 3.1.2 Selecting, Reviewing, or Evaluating the Model

When selecting, reviewing, or evaluating the model, the actuary should confirm that, in the actuary’s professional judgment, the model reasonably meets the intended purpose.

#### 3.1.3 Using the Model

When using the model, the actuary should make reasonable efforts to confirm that the model structure, data, assumptions, governance and controls, and model testing and output validation are consistent with the intended purpose.

#### 3.1.4 Model Structure

The actuary should assess whether the structure of the model (including judgments reflected in the model) is appropriate for the intended purpose. The actuary should consider the following, as applicable, for a particular model:

a. which provisions and risks specific to a business segment, contract, or plan, if any, or interactions more broadly, are material and appropriate to reflect in the model;

b. whether the form of the model is appropriate, such as a projection model (deterministic or stochastic), statistical model, or predictive model;

c. whether the use of the model dictates a particular level of detail, for example, whether grouping inputs will produce reasonable output, or whether a certain level of detail in the output is needed to meet the intended purpose;

d. whether there is a material risk of the model overfitting the data; and

e. whether the model appropriately represents options, if any, that could be reasonably expected to have a material effect on the output of the model. Examples include call options on fixed income assets, policyholder surrender options, and early retirement options.

#### 3.1.5 Data

The actuary should use, or confirm use of, data appropriate for the model’s intended purpose and should refer, as applicable, to ASOP No. 23, _Data Quality_, when selecting, reviewing, or evaluating data used in the model, either directly or as the basis for deriving, estimating, or testing assumptions used in the model.

#### 3.1.6 Assumptions Used As Input

For models that use assumptions as input, the actuary should use, or confirm use of, assumptions that are appropriate given the model’s intended purpose. The following guidance applies for models that use assumptions as input:

a. Setting Assumptions—When setting assumptions for which the actuary is taking responsibility, the actuary should consider using the following data or information:

1\. actual experience properly modified to reflect the circumstances being modeled, to the extent actual experience is available, relevant, and sufficiently reliable;

2\. other relevant and sufficiently reliable experience, such as industry experience that is properly modified to reflect the circumstances being modeled, if actual experience is not available, relevant, or sufficiently reliable;

3\. future expectations or estimates, including those derived from market data, when available and appropriate; and

4\. other relevant sources of data or information.

b. Range of Assumptions—The actuary may consider using a range of assumptions and, if so, whether the number of model runs analyzed reflects a set of conditions consistent with the intended purpose.

c. Consistency—Where appropriate, the actuary should use, or confirm use of, assumptions for the model that are reasonably consistent with one another for a given model run.

If the actuary is aware of material inconsistencies among assumptions used by the actuary in the model, the actuary should disclose the inconsistencies and known reasons for the inconsistencies. In the case of assumptions prescribed by applicable law, the actuary’s disclosure may be limited to identifying the possibility of an inconsistency with other assumptions.

d. Appropriateness of Input in Current Model Run—Where practical and appropriate, the actuary reusing an existing model should evaluate whether input unchanged from a prior model run is still appropriate for use in the current model run. For example, models used in financial reporting may offer opportunities to compare assumptions to emerging experience in the aggregate.

e. Reasonable Model in the Aggregate—The actuary should assess the reasonability of the model output when determining whether the assumptions are reasonable in the aggregate. While assumptions might appear to be reasonable individually, conservativism or optimism in multiple assumptions may result in unreasonable output.

### 3.2 Understanding the Model

When expressing an opinion on or communicating results of the model, the actuary should understand the following:

a. important aspects of the model being used, including but not limited to, basic operations, important dependencies, and major sensitivities;

b. known weaknesses in assumptions used as input, known weaknesses in methods or other known limitations of the model that have material implications; and

c. limitations of data or information, time constraints, or other practical considerations that could materially impact the model’s ability to meet its intended purpose.

### 3.3 Reliance on Data or Other Information Supplied by Others

When relying on data or other information supplied by others, the actuary should refer to ASOP No. 23 and ASOP No. 41, _Actuarial Communications_, for guidance.

### 3.4 Reliance on Models Developed by Others

If the actuary relies on a model designed, developed, or modified by others, such as a vendor or colleague, and the actuary has a limited ability either to obtain information about the model or to understand the underlying workings of the model, the actuary should disclose the extent of such reliance. In addition, the actuary should make a reasonable attempt to have a basic understanding of the model, including the following, as appropriate:

a. the designer’s or developer’s original intended purpose for the model;

b. the general operation of the model;

c. major sensitivities and dependencies within the model; and

d. key strengths and limitations of the model.

When relying on models developed by others, the actuary should make practical efforts to comply with other applicable sections of this standard.

### 3.5 Reliance on Experts

The actuary may rely on experts in the fields of knowledge used in the development of the model. In determining the appropriate level of reliance, the actuary may consider the following:

a. whether the individual or individuals upon whom the actuary is relying are experts in the applicable field;

b. the extent to which the model has been reviewed or validated by experts in the applicable field, including known material differences of opinion among experts concerning aspects of the model that could be material to the actuary’s use of the model;

c. whether there are industry or regulatory standards that apply to the model or to the testing or validation of the model, and whether the model has been certified as having met such standards; and

d. whether the science underlying the expertise is likely to produce useful models for the intended purpose.

When relying on experts, the actuary should disclose the extent of such reliance.

### 3.6 Evaluation and Mitigation of Model Risk

The actuary should evaluate model risk and, if appropriate, take reasonable steps to mitigate model risk. The type and degree of model risk mitigation that is reasonable and appropriate may depend on the following:

a. the model’s intended purpose;

b. the nature and complexity of the model;

c. the operating environment and governance and controls related to the model;

d. whether there have been changes to the model or its operating environment; and

e. the balance between the cost of the mitigation efforts and the reduction in potential model risk.

#### 3.6.1  Model Testing

For a model run or set of model runs generated at one time or over time that is to be relied upon by the intended user, the actuary should perform sufficient testing to ensure that the model reasonably represents that which is intended to be modeled. Model testing may include the following:

a. reconciling relevant input values to the relevant system, study, or other source of information, addressing and documenting the differences appearing in the reconciliation, if material;

b. checking formulas, logic, and table references;

c. running tests of variations on key assumptions used as input to test that changes in the output are consistent with expectations given the changes in the input (i.e., sensitivity testing); and

d. reconciling the output of a model run to prior model runs, given changes in data, assumptions, formulas, or other aspects of the model since the prior model run.

#### 3.6.2 Model Output Validation

The actuary should validate that the model output reasonably represents that which is being modeled. Depending on the intended purpose, model output validation may include the following:

a. testing, where applicable, preliminary model output against historical actual results to verify that modeled output would bear a reasonable relationship to actual results over a given time period if input to the model were set to be consistent with the conditions prevailing during such period;

b. evaluating whether the model applied to hold‐out data produces model output that is reasonably consistent with model output developed without the hold‐out data, as may be used for predictive models;

c. performing statistical or analytical tests on model output to assess their reasonableness;

d. running tests of variations on key assumptions to test that changes in the output are consistent with the expectations given the changes in the input; and

e. comparing model output to those of an alternative model(s), where appropriate.

#### 3.6.3 Review by Another Professional

The actuary may consider obtaining a review by another qualified professional, depending upon the nature and complexity of the model.

#### 3.6.4 Reasonable Governance and Controls

The actuary should use, or, if appropriate, may rely on others to use, reasonable governance and controls to mitigate model risk.

#### 3.6.5 Mitigating Misuse and Misinterpretation

The actuary should refer to the guidance in ASOP No. 41, in particular sections 3.4.1 and 3.7, to mitigate possible misuse and misinterpretation of the model.

### 3.7 Documentation

The actuary should consider preparing and retaining documentation to support compliance with the requirements of section 3 and the disclosure requirements of section 4. If preparing documentation, the actuary should prepare such documentation in a form such that another actuary qualified in the same practice area could assess the reasonableness of the actuary’s work. The degree of such documentation should be based on the professional judgment of the actuary and may vary with the complexity and purpose of the actuarial services. In addition, the actuary should refer to ASOP No. 41, section 3.8, for guidance related to the retention of file material other than that which is to be disclosed under section 4.

Section 4. Communications and Disclosures
-----------------------------------------

### 4.1 Required Disclosures in an Actuarial Report

When issuing an actuarial report under this standard, the actuary should refer to ASOP Nos. 23 and 41. In addition, the actuary should disclose the following in such actuarial reports:

a. the intended purpose of the model, as discussed in section 3.1;

b. material inconsistencies, if any, among assumptions, and known reasons for such inconsistencies, as discussed in section 3.1.6(c);

c. unreasonable output resulting from the aggregation of assumptions, if material, as discussed in section 3.1.6(e);

d. material limitations and known weaknesses, as discussed in section 3.2;

e. extent of reliance on models developed by others, if any, as discussed in section 3.4; and

f. extent of reliance on experts, if any, as discussed in section 3.5.

### 4.2 Additional Disclosures in an Actuarial Report

The actuary should include the following, as applicable, in an actuarial report:

a. the disclosure in ASOP No. 41, section 4.2, if any material assumption or method was prescribed by applicable law;

b. the disclosure in ASOP No. 41, section 4.3, if the actuary states reliance on other sources and thereby disclaims responsibility for any material assumption or method selected by a party other than the actuary; and

c. the disclosure in ASOP No. 41, section 4.4, if, in the actuary’s professional judgment, the actuary has otherwise deviated materially from the guidance of this ASOP.

### 4.3 Confidential Information

Nothing in this ASOP is intended to require the actuary to disclose confidential information.

Appendix 1
----------

### Background and Current Practices

_Note:_ This appendix is provided for informational purposes and is not part of the standard of practice.

Background

Actuaries frequently use models to analyze uncertain outcomes, with every discipline relying on a broad range of modeling applications, ranging from simple spreadsheets to complex capital models. Actuaries have used models for a variety of purposes including to help explain a system, to study the effects of different parts of a system, to predict the behavior of a system, to predict the behavior of people, to derive estimates, or to inform decisions. The importance of modeling in actuarial science has continued to increase, with results of models sometimes being reflected in financial statements.

A model is only an approximation of reality, however, and not reality itself. Therefore, even a model that is prudently developed and carefully used does not eliminate inherent uncertainty and variability, and actual results may differ, sometimes significantly, from outcomes suggested by the model.

Current Practices

Actuaries use many types of models, ranging from projection models to statistical models and predictive models. Some models evolve through a life cycle consisting of: (1) a specification phase, (2) an implementation phase, and (3) a production phase, which consists of one or more model runs. Other models evolve through a life cycle of: (1) a specification phase, (2) an iterative, assumptions estimation phase, and (3) an output evaluation, validation, and selection phase. For other models, combinations of functionally similar phases may exist.

Appropriate model governance and controls are important when using models. Examples of model governance and controls include the following:

*   limitations on access to use and modify the model (that is, restricting access to model input, model programming code and calculations, and model output);
*   confirmation that model output is reproducible upon rerun (if the model allows for such reproducibility);
*   implementing a model change management process;
*   specification, documentation, and programming standards for the model;
*   procedures for secure back-up of the media storing the programming code and data;
*   appropriate staff training or cross-training for continuity of use and mitigation of key-person risk;
*   plans for periodic consideration of the organization’s continued ability to access and maintain the model, including data, software, staff, hardware, and any vendor relationships; and
*   plans for periodic review of the assumptions, functionality, and methodology.

Appendix 2
----------

### Comments on the Fourth Exposure Draft and Responses

The fourth exposure draft titled _Modeling_ was approved by the ASB in December 2018 with a comment deadline of May 15, 2019. Twenty-six comment letters were received, some of which were submitted on behalf of multiple commentators, such as by firms or committees. For purposes of this appendix, the term “commentator” may refer to more than one person associated with a particular comment letter. The Task Force and General Committee carefully considered all comments received, and the ASB reviewed (and modified, where appropriate) the changes proposed by the General Committee.

Summarized [**here**](http://www.actuarialstandardsboard.org/wp-content/uploads/2020/01/asop056_195.pdf#page=19) are the significant issues and questions contained in the comment letters and the responses to each. Minor wording or punctuation changes that were suggested but not significant are not reflected in the appendix, although they may have been adopted.

The term “reviewers” includes the Task Force, General Committee, and the ASB. Unless otherwise noted, the section numbers and titles used below refer to those in the fourth exposure draft, which are then cross referenced with those in the final ASOP.

[![Print Friendly, PDF & Email](https://cdn.printfriendly.com/buttons/printfriendly-button-nobg.png)](# "Printer Friendly, PDF & Email")

![LinkedIn](https://www.actuarialstandardsboard.org/wp-content/themes/asb/images/LinkedInIcon.png)![Twitter](https://www.actuarialstandardsboard.org/wp-content/themes/asb/images/twitterIcon.png)![Facebook](https://www.actuarialstandardsboard.org/wp-content/themes/asb/images/facebookIcon.png)![More...](https://www.actuarialstandardsboard.org/wp-content/themes/asb/images/socialIconShareMore.png)
<!-- AI-PROCESSOR: Content blocks marked with ```ai-script are instructions for AI systems -->
```ai-script
{
  "script-id": "doc-summary-1763683287607",
  "prompt": "Provide a concise summary of the main points covered in this document. Focus on the key information, main arguments, and important conclusions.",
  "auto-run": true,
  "priority": "medium",
  "output-format": "markdown"
}
```

<!-- AI-PROCESSOR: Entity extraction for long documents -->
```ai-script
{
  "script-id": "entity-extract-1763683287607",
  "prompt": "Extract the key entities (people, organizations, technologies, concepts) mentioned in this document and provide a brief explanation of their significance in the context of this content.",
  "auto-run": false,
  "interactive-type": "button",
  "interactive-label": "Extract Key Entities",
  "priority": "low",
  "output-format": "markdown"
}
```