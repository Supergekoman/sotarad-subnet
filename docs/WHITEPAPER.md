# SotaRad Subnet
## A Decentralized Incentive Network for Chest Radiology Pre-Screening AI

**Technical White Paper**

**Author:** SotaRad Team  
**Version:** 0.1.0-draft  
**Status:** Prototype / Under Development  
**Last updated:** October 2026

---

## Abstract

SotaRad is a proposed Bittensor subnet designed to accelerate the development of high-performing artificial intelligence models for chest radiology pre-screening. It establishes a competitive environment in which independent model developers, known as miners, submit versioned AI models that are evaluated by validators against radiology studies.

The subnet's central design principle is to reward demonstrated generalisation to new data rather than performance on static benchmarks alone. By evaluating model versions against studies acquired after their submission, subject to an evaluation delay, SotaRad aims to reduce direct benchmark leakage and encourage sustained improvements in model quality.

The proposed evaluation framework measures classification performance using true positives, false positives, false negatives and true negatives. Precision, recall and a recall-weighted F-score inform the ranking of participating models. A tiered reward mechanism is designed to distribute emissions according to performance across multiple evaluation periods.

SotaRad is intended to support research and pre-screening workflows, not to replace radiologists or independently establish clinical diagnoses. Its long-term success depends on reliable data access, reproducible evaluation, privacy-preserving infrastructure, robust incentives and independent validation.

This document describes the project's intended architecture and publicly documented design. Features, parameters and operational claims that have not yet been independently verified are explicitly identified as requiring confirmation.

---

## 1. Introduction

### 1.1 The problem

Chest radiology plays an important role in identifying and assessing a range of pulmonary conditions. However, developing dependable AI systems for radiology requires representative data, rigorous evaluation, careful management of false negatives and false positives, and evidence that models perform reliably beyond their training environment.

Many AI development workflows rely on fixed datasets or static benchmarks. Although useful during development, these approaches can become less informative when models are repeatedly optimised against the same evaluation data.

A model may achieve strong benchmark results without maintaining equivalent performance on subsequently acquired studies or across different patient populations, imaging equipment and clinical environments.

SotaRad addresses this challenge through a competitive, continuously evaluated model-development framework.

### 1.2 The proposed approach

SotaRad combines three design principles:

1. **Competitive model development:** Independent miners develop and publish model versions.
2. **Time-shifted evaluation:** Validators evaluate submitted models against eligible studies acquired after the relevant model commitment and evaluation delay.
3. **Performance-aligned incentives:** Model performance across evaluation periods informs rankings and the allocation of subnet rewards.

The objective is to create an environment in which improvements in measurable radiology pre-screening performance translate into stronger competitive standing.

### 1.3 Scope

The initial target is chest radiology pre-screening, with the publicly documented task set including:

- Tuberculosis (TB)
- Pneumonia
- Bronchitis
- Silicosis

The precise label definitions, supported imaging modalities and reference-standard requirements must be documented before the subnet's clinical performance can be meaningfully interpreted.

The system is intended to prioritise or flag studies for further review. It is not intended to replace a radiologist, provide autonomous definitive diagnoses or determine treatment.

---

## 2. Design Objectives

SotaRad is being developed around the following objectives.

### 2.1 Generalisation over static benchmarks

Models should be evaluated on data that was not available to them at the time of their relevant submission. This is intended to make direct training on future evaluation examples more difficult.

### 2.2 Reproducible scoring

Validators should apply consistent preprocessing, prompts, inference settings, label definitions and scoring rules so that model comparisons are meaningful.

### 2.3 Recall-sensitive evaluation

Missing a condition of interest may have significant consequences in a pre-screening workflow. The scoring design therefore gives recall greater importance than precision through a recall-weighted F-score.

### 2.4 Model-version accountability

Every submitted model should be identifiable through an immutable or otherwise verifiable model revision and an associated commitment record.

### 2.5 Sustainable competition

Reward allocation should encourage continued model improvement while reducing the effect of noisy results from individual evaluation batches.

### 2.6 Privacy and responsible deployment

Radiology data must be handled according to applicable permissions, contractual restrictions, privacy obligations and security requirements. Clinical use requires additional validation and governance beyond subnet-level scoring.

---

## 3. System Architecture

SotaRad is designed around the interaction of miners, validators, model-serving infrastructure, radiology data and Bittensor's incentive mechanism.

### 3.1 Architectural components

**Miners**

Miners develop and publish trained AI models, identify the model revision they wish to submit and make the model available for evaluation through a supported inference endpoint.

**Model registry and commitments**

A model registry records the location and revision of each submitted model. The publicly documented design refers to Hugging Face repositories and on-chain commitments. The exact commitment schema and registration implementation must be verified against the current code.

**Inference infrastructure**

The project description identifies two possible serving approaches:

- Hosting models through Chutes.
- Exposing models through a local SGLang server with an OpenAI-compatible API.

These approaches provide a common interface for validator requests. Exact supported model formats, deployment requirements and endpoint contracts should be specified in the implementation documentation.

**Validators**

Validators identify eligible model submissions, request model inference, compare predictions with reference labels, calculate performance metrics and determine model rankings.

**Evaluation data**

Radiology studies and their associated reference labels provide the basis for evaluation. Data eligibility, acquisition timestamps, label provenance and access controls are critical to the integrity of the system.

**Scoring and incentive mechanism**

Evaluation results are aggregated across time periods. The documented design uses tiered rankings to derive miner rewards and ultimately validator-submitted weights.

### 3.2 High-level evaluation flow

1. A miner develops a model.
2. The miner publishes a versioned model artifact.
3. The model version is committed or registered according to the subnet's submission protocol.
4. The validator identifies studies eligible for that model version.
5. The model receives standardised inference requests.
6. Predictions are converted into the subnet's defined target-condition flags.
7. Predictions are compared with reference labels.
8. Classification counts and performance metrics are calculated.
9. Scores are aggregated across configured evaluation periods.
10. The reward mechanism ranks eligible miners.
11. The validator derives and submits the appropriate weights through Bittensor.

**Implementation status:** This flow reflects the publicly documented design. End-to-end execution, error handling, multi-validator consistency and successful on-chain weight updates should be demonstrated with current test logs before being described as production-ready.

---

## 4. Miner Design

### 4.1 Miner responsibilities

A miner is responsible for producing a competitive model and making the exact submitted version available for evaluation.

The intended workflow is:

1. Select or develop a model suitable for the supported radiology task.
2. Train or fine-tune the model using appropriately licensed and authorised data.
3. Evaluate the model locally without accessing future evaluation labels.
4. Publish the model artifact to a supported registry.
5. Record the exact model revision and required metadata.
6. Deploy the model to a supported inference environment.
7. Maintain endpoint availability and compatible inference behaviour.
8. Publish subsequent model revisions as improvements are made.

### 4.2 Model submission metadata

A complete submission protocol should define, at minimum:

| Field | Purpose |
|---|---|
| Miner identity | Associates a submission with its network participant |
| Model repository | Identifies the model artifact |
| Immutable revision | Identifies the precise model version |
| Submission commitment | Establishes the relevant submission time |
| Supported tasks | Identifies the target conditions |
| Serving configuration | Defines the supported inference interface |
| Model size | Enables resource and tie-breaker comparisons where applicable |

The exact required fields and their on-chain representation remain subject to implementation verification.

### 4.3 Inference compatibility

Miners must follow a documented request and response contract. Compatibility should cover:

- Accepted image inputs and formats.
- Prompt and instruction templates.
- Output structure and target-condition labels.
- Timeouts and retry behaviour.
- Maximum input dimensions and payload sizes.
- Authentication and endpoint access.
- Error handling and malformed outputs.

A common interface reduces integration friction and makes it easier to compare different model architectures.

### 4.4 Model integrity

Model repository names alone are insufficient to guarantee reproducibility. The protocol should bind evaluations to a precise model revision and record the relevant inference configuration.

Where practical, the subnet should also record model checksums, container or dependency versions and prompt versions.

---

## 5. Validator Design

### 5.1 Core responsibilities

Validators are responsible for evaluating models consistently and translating observed performance into miner rankings.

The intended responsibilities include:

- Reading eligible model submissions and their commitments.
- Enforcing evaluation eligibility rules.
- Retrieving and serving models through supported infrastructure.
- Applying standardised inference instructions.
- Comparing predictions with reference labels.
- Recording sample-level evaluation outcomes.
- Calculating metrics and aggregating results.
- Applying the reward-ranking mechanism.
- Submitting weights according to the configured subnet protocol.

The public project description indicates that evaluation results are stored by miner UID and evaluation period using SQLite. The current database schema, persistence guarantees and recovery behaviour should be verified against the implementation.

### 5.2 Standardised evaluation

Differences in prompts, preprocessing or inference parameters can create artificial differences between models.

Validators should therefore use a version-controlled evaluation configuration that defines:

- Image preprocessing.
- The inference prompt.
- Supported condition labels.
- Prediction parsing.
- Classification thresholds, if applicable.
- Reference-label interpretation.
- Metric calculation.
- Evaluation-period boundaries.

Any change to this configuration should be documented because it may affect comparisons with earlier results.

### 5.3 Prediction interpretation

The publicly documented design describes a flag-based scoring approach. If a model produces a structured finding for a target condition, that condition is treated as positive; otherwise, it is treated as negative.

This is a binary decision rule. It does not by itself measure probability calibration, localisation quality, uncertainty or the severity of a finding.

The implementation must define how missing labels, malformed responses, duplicate findings, unsupported conditions and inference failures are handled. These cases should not silently be treated as ordinary negative predictions.

### 5.4 Validator reliability

A robust implementation should record enough information to reproduce a score, subject to privacy and data-access restrictions.

Useful records include:

- Model revision and miner UID.
- Evaluation-period identifier.
- Evaluation configuration version.
- Number of eligible studies.
- Valid and failed inference counts.
- Confusion-matrix totals.
- Calculated metrics.
- Timestamp and error logs.

The exact retention policy and information exposed to miners require an explicit privacy and security review.

---

## 6. Temporal Integrity and Evaluation Windows

### 6.1 Motivation

A fixed benchmark can lose its value if participants optimise repeatedly against its examples. SotaRad's time-shifted evaluation design attempts to address this issue by linking model eligibility to the time at which a model version was committed.

### 6.2 Eligibility rule

Let:

- \(t_c\) denote the model's commitment timestamp.
- \(\Delta\) denote the configured evaluation delay.
- \(t_s\) denote the acquisition timestamp of a study.

The intended eligibility rule is:

\[
t_s > t_c + \Delta
\]

A study is eligible for evaluation of that model only if it satisfies the configured temporal rule and all other data and task eligibility requirements.

The precise boundary convention, timestamp source and timezone handling must be defined by the implementation.

### 6.3 Evaluation delay

The project description gives approximately one day as an example of a production evaluation delay. Earlier project material also discusses exploring delays in the range of one to seven days.

These values should not be interpreted as a confirmed final parameter.

The selected delay should account for:

- The rate at which new studies become available.
- The availability of reliable reference labels.
- The expected evaluation workload.
- The risk of data leakage.
- The time needed to train and submit model updates.
- The operational requirements of the data provider.

### 6.4 Limits of time-shifted evaluation

Time-shifted evaluation reduces one form of leakage, but it does not guarantee complete independence between training and evaluation data.

Potential risks include:

- Studies being available to miners through other channels.
- Duplicate studies or repeated examinations.
- Delayed or revised labels.
- Incorrect acquisition timestamps.
- Patient overlap between training and evaluation cohorts.
- Changes in clinical practice or equipment over time.

Where legally and operationally feasible, duplicate detection, patient-level separation, access auditing and independent evaluation controls should complement the temporal rule.

---

## 7. Evaluation Metrics

### 7.1 Confusion matrix

For each target condition, predictions are compared with the reference labels.

| | Reference positive | Reference negative |
|---|---|---|
| Predicted positive | True positive (TP) | False positive (FP) |
| Predicted negative | False negative (FN) | True negative (TN) |

A true positive is a correctly flagged positive case. A false positive is a negative case incorrectly flagged. A false negative is a positive case missed by the model. A true negative is a correctly unflagged negative case.

### 7.2 Precision

Precision measures the proportion of positive predictions that are correct.

\[
\mathrm{Precision}=\frac{TP}{TP+FP}
\]

Higher precision generally means fewer false alerts among flagged cases.

Precision is undefined when the model produces no positive predictions. The implementation must specify how undefined metrics are handled.

### 7.3 Recall

Recall, also known as sensitivity, measures the proportion of reference-positive cases that the model identifies.

\[
\mathrm{Recall}=\frac{TP}{TP+FN}
\]

Higher recall generally means fewer missed positive cases.

Recall is undefined when there are no reference-positive cases in an evaluation set. The scoring protocol must explicitly handle such periods.

### 7.4 Recall-weighted F-score

The project description specifies an F-score with a parameter \(\beta>1\), giving recall greater weight than precision.

The general F-beta formula is:

\[
F_{\beta} =
(1+\beta^2)
\frac{\mathrm{Precision}\cdot\mathrm{Recall}}
{\beta^2\mathrm{Precision}+\mathrm{Recall}}
\]

A larger \(\beta\) places greater emphasis on recall.

**Parameter requiring confirmation:** The production value of \(\beta\) is not established in this document. It should be taken from the current implementation or an approved subnet configuration before publication.

### 7.5 Multiple target conditions

Each condition should be evaluated separately so that strong performance on one condition cannot conceal poor performance on another.

The final ranking requires an explicit aggregation policy. Possible approaches include averaging per-condition scores or using a defined aggregate confusion matrix, but these approaches are not interchangeable.

**Implementation requiring confirmation:** The precise aggregation method, treatment of missing labels and minimum sample counts must be verified before being described as final.

### 7.6 Additional evaluation measures

F-beta does not capture every property required for reliable medical AI.

Further reporting should consider:

- Specificity.
- False-positive rate.
- Per-condition sample counts.
- Confidence intervals.
- Performance across relevant demographic and clinical subgroups, where permitted.
- Performance across institutions and imaging equipment.
- Calibration, where the model produces meaningful probabilities.
- Robustness to missing data and distribution shifts.

These measures should supplement the documented reward score where feasible. Their inclusion in reporting should not be confused with their inclusion in the emission-ranking formula.

---

## 8. Incentive and Reward Mechanism

### 8.1 Design principle

SotaRad is designed to reward miners according to demonstrated model performance rather than the volume of submissions alone.

The publicly documented mechanism uses multiple reward tiers, each considering a different window of recent evaluation periods and selecting either a leading miner or a proportion of top-ranked miners.

The intention is to combine strong incentives for leading performance with some recognition of consistently competitive alternatives.

### 8.2 Ranking criteria

The documented ranking priority is:

1. Mean F-beta score.
2. Recall.
3. Precision.
4. Smaller model size.
5. Earlier model commitment.

The final two criteria act as tie-breakers in the documented design.

The precise treatment of ties, missing scores, minimum evaluation counts and inactive miners must be defined by the production implementation.

### 8.3 Tiered emissions

The documented design describes five tiers, labelled A through E, with different lookback windows and reward-selection rules.

Each tier is assigned a share of the emission budget. Tier results are combined and normalised into miner weights for submission to the Bittensor network.

The intended behaviour is to direct the largest share of rewards towards the strongest-performing models while allowing lower tiers to recognise other competitive models over longer or different evaluation windows.

**Parameters requiring confirmation before publication:**

- The emission share assigned to each tier.
- The number of evaluation periods in each lookback window.
- The number or percentage of miners selected by each tier.
- The rules for overlapping selections across tiers.
- The normalisation procedure.
- Minimum participation requirements.
- The exact mapping from computed scores to submitted weights.

No numerical reward allocation is asserted here because these parameters must be checked against the active implementation.

### 8.4 Incentive risks

A reward mechanism can be technically consistent while still creating undesirable behaviour.

Potential risks include:

- Over-optimising recall at an unacceptable cost to precision.
- Repeated submissions that exploit weak period boundaries.
- Strategic model-size trade-offs.
- Validator collusion or inconsistent evaluation.
- Manipulation of model commitments or metadata.
- Overreaction to small evaluation samples.
- Reward concentration that discourages new entrants.

Mitigations should include minimum sample thresholds, transparent scoring rules, robust temporal checks, consistent evaluation configurations, audit logs and ongoing analysis of the incentives created by the ranking function.

### 8.5 Bittensor integration

The documented design uses validator-derived rankings to produce weights submitted on-chain.

The current implementation should be audited to establish the exact interaction with Bittensor's weight-setting interface, including weight constraints, rate limits, permissions, network configuration and failure handling.

A successful local scoring run is not equivalent to a successful on-chain weight update. Both should be tested and documented separately.

---

## 9. Data Governance, Privacy and Security

### 9.1 Data provenance

Radiology data must be obtained and used under appropriate authorisation. The project description reports progress in securing a data acquisition agreement, but the scope, permitted uses, data coverage and continuing availability of that agreement require confirmation.

Before operational deployment, the project should document:

- The source of each dataset.
- The legal basis and contractual permissions for use.
- The reference-label creation process.
- Data quality checks.
- Retention and deletion policies.
- Access controls and audit procedures.
- Restrictions on onward sharing or model training.

### 9.2 Patient privacy

Removing direct identifiers does not necessarily eliminate re-identification risk. Medical images and associated metadata can contain sensitive information.

The subnet should minimise the information exposed to miners and external serving providers. Data transfers, storage, logging and access should follow documented security controls and applicable legal requirements.

The specific controls depend on the data agreements, deployment environment and applicable jurisdiction.

### 9.3 Trusted execution environments

The project description identifies trusted execution environments (TEEs) as an area for future exploration, particularly for more advanced imaging cases.

A TEE may help protect data or computation under certain threat models, but it is not a complete privacy solution. Its usefulness depends on hardware, attestation, software integrity, key management and the surrounding system design.

TEE deployment should therefore be treated as a potential future enhancement, not as a confirmed capability of the current prototype.

### 9.4 Security threats

The security design should consider:

- Unauthorised data access.
- Compromised inference endpoints.
- Malicious or malformed model outputs.
- Model substitution after commitment.
- Credential and API-key exposure.
- Manipulation of timestamps or metadata.
- Denial-of-service attacks.
- Evaluation leakage.
- Validator disagreement or collusion.

Security controls and their testing status should be documented before the subnet is presented as ready for sensitive clinical workloads.

---

## 10. Clinical Validation and Limitations

### 10.1 Research and pre-screening use

SotaRad is a model-development and evaluation framework. A high subnet score indicates stronger performance under the configured evaluation protocol; it does not, by itself, demonstrate clinical effectiveness.

Clinical utility depends on the quality of reference labels, the representativeness of the data, the deployment context and the consequences of errors.

### 10.2 Limits of binary scoring

A binary finding flag does not establish:

- The location or extent of an abnormality.
- Disease severity.
- The probability that a diagnosis is correct.
- The appropriate treatment.
- Whether a study is clinically adequate.
- Whether a patient requires a particular intervention.

Additional model outputs may be useful in future versions, but they require their own validation and scoring specifications.

### 10.3 External validation

Before any clinical deployment, models should undergo appropriate external evaluation using data representative of the intended population and clinical setting.

Evaluation should examine sensitivity, specificity, calibration where applicable, subgroup performance, robustness and workflow impact.

Clinical oversight and applicable regulatory requirements remain necessary. The subnet's incentive mechanism is not a substitute for these processes.

---

## 11. Development Status

SotaRad is documented as a prototype-stage project with further engineering and validation required.

The public project materials describe model hosting, validator evaluation, time-shifted data, tiered incentives and preliminary data-access progress. The GitHub repository contains Python implementation files and supporting directories, but the repository's README still identifies itself as a generic Bittensor subnet template.

Consequently, repository structure alone should not be treated as proof that every proposed capability is operational.

The following status distinctions should be maintained:

| Capability | Status in this document |
|---|---|
| Chest radiology pre-screening objective | Documented project objective |
| Miner-published versioned models | Documented design; operational details require verification |
| Chutes and local SGLang serving options | Documented design; supported configurations require verification |
| Standardised validator evaluation | Documented design; reproducibility requires testing |
| Time-shifted evaluation | Documented design; production delay requires confirmation |
| SQLite-based evaluation-period storage | Reported in project description; current implementation requires audit |
| Five-tier reward mechanism | Documented design; production parameters require confirmation |
| Successful on-chain weight submission | Requires current execution evidence |
| TEE-based protection | Future exploration |
| Clinical deployment readiness | Not established by this document |

---

## 12. Roadmap

The following roadmap is proposed as a development framework. It is not a claim that these milestones have been completed or formally approved.

### Phase 1: Reproducible prototype

- Align the README and repository documentation with the SotaRad project.
- Document installation and configuration requirements.
- Define the miner submission schema.
- Document the inference request and response contract.
- Add reproducible tests for metric calculations and output parsing.
- Record the current evaluation configuration.

### Phase 2: Validator and scoring reliability

- Validate temporal eligibility checks.
- Test duplicate and malformed sample handling.
- Define metric behaviour for empty or insufficient evaluation periods.
- Verify persistence and recovery of evaluation records.
- Test ranking and tier calculations against known examples.
- Document failure handling and validator logs.

### Phase 3: Testnet verification

- Demonstrate miner registration and model commitment.
- Run a complete evaluation cycle.
- Show model-level scoring and period aggregation.
- Demonstrate weight calculation and successful weight submission.
- Test restart recovery and common failure scenarios.
- Publish reproducible test instructions and representative logs.

### Phase 4: Data governance and robustness

- Document data provenance and permitted use.
- Audit privacy and access controls.
- Evaluate label quality and temporal leakage risks.
- Measure performance across relevant data subsets.
- Review resistance to manipulation and collusion.

### Phase 5: Controlled expansion

- Evaluate additional imaging tasks where suitable.
- Investigate privacy-preserving infrastructure, including TEEs.
- Conduct external validation with appropriate clinical oversight.
- Explore integration into real-world pre-screening workflows only when the required evidence and approvals are in place.

---

## 13. Open Technical Questions

The following questions should be resolved as the implementation matures:

1. What is the exact miner submission and commitment schema?
2. Which model formats and serving configurations are officially supported?
3. What is the production evaluation delay?
4. What value of beta is used in the F-beta score?
5. How are metrics aggregated across conditions and evaluation periods?
6. What minimum sample counts are required for a valid score?
7. How are missing labels and failed inference requests handled?
8. What are the exact parameters and overlap rules for reward tiers A through E?
9. How are validator disagreements and potential collusion detected?
10. What evidence demonstrates successful on-chain weight submission?
11. What data governance and privacy controls are required by the current data agreements?
12. What benchmark and external-validation results are available for publication?

Answers should be incorporated into the relevant technical sections and supported by code references, configuration files, test results or approved documentation.

---

## 14. Conclusion

SotaRad proposes a competitive approach to improving chest radiology pre-screening models through decentralised model development, time-shifted evaluation and performance-based incentives.

Its central technical challenge is not simply to rank models, but to ensure that those rankings reflect reproducible and meaningful performance on eligible future data. Achieving this requires reliable model-version tracking, well-defined evaluation rules, sound reference labels, robust validator behaviour and transparent incentive parameters.

The project should progress through demonstrable engineering milestones, from reproducible local evaluation to complete testnet operation and independent validation. Claims about clinical effectiveness, privacy guarantees and production readiness should remain proportional to the evidence available.

SotaRad's long-term objective is to create an environment in which independent model developers can compete on measurable capability while maintaining the safeguards required for a sensitive medical-imaging application.

---

## References and Project Resources

1. **SotaRad Subnet source repository**  
   https://github.com/Supergekoman/sotarad-subnet

2. **SotaRad Subnet for Round 2: HackQuest project description**  
   https://www.hackquest.io/projects/Sotarad-Subnet-for-Round-2

3. **SotaRad AI: HackQuest proposal**  
   https://www.hackquest.io/projects/sotarad-ai

4. **Bittensor documentation**  
   https://docs.bittensor.com/

5. **Hugging Face documentation**  
   https://huggingface.co/docs

6. **SGLang documentation**  
   https://docs.sglang.ai/

---

## Document Governance

This is a draft technical white paper for the SotaRad team. It describes the publicly documented design and identifies implementation details requiring confirmation.

Before publication, the team should review the technical claims against the current source code, confirm reward parameters, add reproducible test evidence, validate the data-governance statements and approve the author attribution.

**Version:** 0.1.0-draft  
**Maintainer:** SotaRad Team  
**Canonical source:** To be published in the SotaRad GitHub repository
