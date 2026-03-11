# Brain-Based-Wheelchair-Control

This project models and tests a **Classifier Factory System** composed of multiple interacting subsystems that collaboratively manage data ingestion, processing, classifier training, validation, and production deployment.

---

## Main Processes

- **Configure Service:**  
  Adjusts classifier parameters (e.g., number of labels) following performance issues. After a configuration update, the system must be restarted.

- **Prepare Session:**  
  Handles time-series helmet records by validating, interpolating missing data, and correcting outliers. Invalid sessions are discarded. Extracted features and labels form the input for subsequent phases.

- **Generate Learning Sets:**  
  Waits for sufficient session data and checks validity and coverage. Data is retained between iterations, allowing gradual correction as new sessions arrive.

- **Develop Classifier:**  
Trains classifiers on the available learning sets and repeats training with different hyperparameters. Only the best-performing classifier instance is passed to validation, which confirms its suitability before deployment.

- **Classify Session:**  
  Uses the deployed classifier in the **Production System** to assign labels to unseen prepared sessions. Each incoming session is validated, passed through the model, and classified.

- **Evaluate Classifier Performance:**  
  Compares predicted labels with expert-provided labels during the evaluation phase. If performance is poor or label coverage is insufficient, the system can trigger a new development phase.

---

## Data and Logic

All configuration parameters are defined in editable **JSON files**, read during application startup to initialize system parameters. The process is user-managed and does not require automated sequence modeling.

---

## Testing & Reliability

The system was tested for **non-interoperability** and **non-resiliency** across six subsystems: Ingestion, Preparation, Segregation, Development, Production, and Evaluation.  
Failures such as missing labels, incorrect schemas, or invalid values were detected and scored based on severity.  
Additional responsiveness and elasticity tests measured the system’s capacity to handle stress and concurrent operations.

---

## Elasticity and Performance

### Development Phase  
Under distributed and controlled environments, performance tests showed that the **average response time grows linearly** with the number of classifiers developed.  
This indicates a consistent development time per classifier — the bottleneck lies in the **ingestion system**, which maintains constant processing speed.

### Production Phase  
Elasticity tests revealed that response time per session grows **linearly up to 70 sessions**, then increases sharply as memory swapping begins.  
The **production system** becomes the bottleneck beyond this threshold.  
The system fails to meet the 0.5 s per-session responsiveness target due to network and hardware latency.

---

**Summary:**  
The classifier factory demonstrates stable, predictable scalability during development but limited real-time responsiveness in production.  
Future improvements should target **memory optimization**, **parallel model loading**, and **network latency reduction** to achieve full responsiveness.

