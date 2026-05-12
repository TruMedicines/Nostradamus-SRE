# Development Specification: NOSTRADAMUS v16.2

## 1. System Overview

NOSTRADAMUS is a high-precision Software Reliability Engineering (SRE) tool. It ingests software failure data and applies mathematical growth models to predict future system behavior, total latent defect counts, and deployment readiness. It is designed for use by Quality Assurance (QA) leads and Reliability Engineers. 

## 2. Visual Architecture

The application utilizes an **Industrial White UI** designed for professional engineering environments, emphasizing high contrast and zero visual clutter. 

- **Header:** Branding and "Verified Operational" status indicator. 

- **Sidebar (360px):** Fixed-width control center for model selection, parameter estimation, and data ingestion. 

- **Main Workspace:** Fluid-responsive area housing high-resolution HTML5 Canvas renders of **μ(t)** (Cumulative Failures) and **λ(t)** (Failure Intensity). 

- **Data Viewport:** A specialized text container exactly **110px (approx. 1 inch) high** to ensure data visibility while maintaining sidebar ergonomics. 

## 3. Data Specification & Structure

### 3.1 The .dat File Structure

The engine processes **Time-Between-Failure (TBF)** data formatted as a flat-file stream. 

- **Format:** Comma-delimited (CSV style). 

- **Content:** Numerical values representing the time elapsed since the previous failure. 

- **Example:** 0.05, 0.12, 0.18, 0.25, 0.33. 

### 3.2 Volume & Gathering

- **Minimum Data:** 3 points (8–12 recommended for statistical stability). 

- **Maximum Data:** 1,000 points (Engine limit for visual curve fidelity). 

- **Data Collection:** Engineers should record execution time or CPU time between observed anomalies during the system test phase. 

## 4. Functional Logic (The Engine)

### 4.1 Estimation Methods

The tool allows the user to choose how the mathematical model "fits" the raw data using two distinct statistical approaches: 

| **Method** | **Full Name** | **Technical Logic** |
| - | - | - |
| **MLE** | **Maximum Likelihood Estimation** | Finds parameter values that maximize the likelihood of making the observations given the underlying probability distribution. |
| **LSE** | **Least Squares Estimation** | Minimizes the sum of the squares of the offsets (residuals) between the actual data points and the fitted curve. |

**Mathematical Formulations:**

- **MLE:** $\\hat\{\\theta\}\_\{MLE\} = \\arg\\max\_\{\\theta\} \\mathcal\{L\}(\\theta; \\mathbf\{x\})$ 

- **LSE:** $S = \\sum\_\{i=1\}^\{n\} (y\_i - f(x\_i, \\beta))^2$ 

### 4.2 Analytical Models (Expanded Library)

The v16.2 engine supports ten distinct reliability models:

1. **Jelinski-Moranda (JM):** Assumes a constant hazard rate; each bug fix reduces the total failure rate by a constant amount. 

2. **Geometric (GE):** Accounts for learning effects where early bugs are discovered faster than later ones.

3. **Log-Poisson (LP):** Best for mature software; discovery rates decrease exponentially as testing continues. 

4. **Goel-Okumoto (GO):** An NHPP model based on the assumption that failures occur in a non-homogeneous Poisson process.

5. **Schick-Wolverton (SW):** Assumes the failure rate is proportional to the number of remaining defects and the time spent in testing.

6. **Musa Basic (MB):** Uses execution time to model the discovery of faults and the decrease in failure intensity.

7. **Littlewood-Verrall (LV):** A Bayesian model that accounts for the uncertainty in the reliability growth process.

8. **Brooks-Motley (BM):** Models fault discovery across different program modules or test phases.

9. **Delayed S-Shape (DS):** Models a "learning curve" where bug discovery starts slow, peaks, and then tapers off. 

10. **Markov Chain (MC):** Simulates reliability through discrete state transitions, predicting the probability of the system reaching a "Hardened" steady state.

## 5. Results & Interpretation

### 5.1 Metrics Display (Section 4)

Provides instant feedback on the active model, the **Estimated Total (N)** (total bugs likely to exist in the code), and the **Prediction Window** (time horizon for future reliability). 

### 5.2 Dynamic Interpretation (Section 5)

A context-aware summary that translates mathematical trends into plain English. For instance, the Markov Chain model interpretation describes the probability of transitioning from a failed state to an operational steady state. 

## 6. Technical Requirements

- **Platform:** Client-side HTML5/ES6 (No server-side processing for IP security). 

- **Scaling:** Flexbox-driven layout with min-height: 0 constraints to prevent graph overlap in full-screen or high-resolution modes. 

- **Security:** 100% Local execution; failure data never leaves the user's machine. 

