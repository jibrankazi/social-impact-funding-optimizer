# Social Impact Funding Optimizer

## Problem Statement and Goals

Organizations that fund social programs often need to decide how to distribute limited resources across multiple proposals.  The **Social Impact Funding Optimizer** helps maximize social return by selecting the combination of projects that delivers the greatest overall benefit under a fixed budget.  Key objectives include:

- **Maximize social outcomes:** use predictive models to estimate the expected impact of each investment and allocate funds to maximize the sum of impacts.
- **Efficient resource use:** ensure the limited budget is spent on the most effective mix of projects or funding levels.
- **Data‑driven decision making:** replace ad‑hoc or purely subjective decisions with a reproducible approach informed by historical data and optimization models.

## Solution Overview and Methodology

The optimizer integrates two analytic components:

1. **Predictive model:** Using historical data on past grants, we train a machine‑learning model (e.g., regression or classification via scikit‑learn) to estimate the impact of funding a project.  Features might include requested amount, project sector, region need indicators and past performance.  The model outputs a numeric impact score for each candidate project.
2. **Optimization model:** We formulate a linear or mixed‑integer programming problem where decision variables represent whether (or how much) to fund each project.  The objective maximizes the total predicted impact subject to constraints such as a total budget and any business rules (e.g. minimum allocations by sector).  We use **PuLP** as the default solver library because it is lightweight and includes the CBC solver.  For more complex scenarios, you can uncomment optional dependencies (Pyomo or OR‑Tools) and adjust the solver.

The combination of predicted impact scores and mathematical optimization ensures funds are directed where they deliver the highest expected return.  The optimizer can be extended with additional constraints or alternative models as needed.

## Installation

1. Clone this repository:

   ```bash
   git clone https://github.com/jibrankazi/social-impact-funding-optimizer.git
   cd social-impact-funding-optimizer
   ```

2. Create and activate a Python virtual environment (Python 3.8 or later is recommended):

   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

3. Install the required packages:

   ```bash
   pip install -r requirements.txt
   ```

   The core dependencies include **pandas**, **numpy**, **scikit‑learn**, **pulp**, **matplotlib** and **seaborn**.  Optional libraries such as Pyomo, OR‑Tools, Plotly, Bokeh and Streamlit are commented out in `requirements.txt`.  Uncomment and install them if you plan to use advanced solvers, interactive charts or build a web app.

## Usage Instructions

1. **Prepare your data:** Create a CSV or other dataset where each row corresponds to a candidate project.  Include the project identifier, requested funding amount and any features used by the predictive model (e.g., sector, target population, need indicators).  Historic projects with known outcomes are needed to train the model.

2. **Train or load the predictive model:** Use a provided notebook or script (e.g. `model_training.ipynb`) to train a regression/classifier on historical data.  Save the trained model to disk (e.g. using `joblib`) or run the model directly on your current proposals to generate predicted impact scores.

3. **Run the optimization:** Execute the optimization script (e.g. `optimizer.py` or an `optimize_allocation.py` file) providing the predicted impact scores and costs.  The script defines decision variables, sets up the objective and budget constraint, and solves for the optimal portfolio using PuLP.  The output lists which projects to fund and their expected impact, along with budget utilization.  You can adjust parameters such as the total budget and add extra constraints by modifying the script.

4. **Review and iterate:** Examine the recommended funding plan.  Visualize the results using matplotlib/seaborn or any preferred library; for example, a bar chart showing funds allocated by sector, or a scatter plot of impact vs. cost with funded projects highlighted.  If stakeholders have additional requirements (e.g., reserve a minimum share for certain communities), modify the model and re‑run the optimization.  Always treat the recommendation as a decision support tool and consider qualitative factors not captured in the data.

5. **(Optional) Launch a web app:** If Streamlit is installed and an `app.py` script is provided, run `streamlit run app.py` to launch an interactive interface.  This allows non‑technical users to input the available budget, toggle constraints and view the optimized funding plan in a browser.

## Interpreting the Output

The optimizer provides a quantitative recommendation, but it does not replace human judgement.  Consider the following when interpreting results:

- **Model assumptions:** Predictions are only as good as the training data.  If the historical data is limited or biased, adjust the model accordingly or perform sensitivity analysis.
- **Unmodelled factors:** The tool cannot capture qualitative considerations (e.g. political priorities, innovation potential).  Use the results as a starting point for discussions rather than a final verdict.
- **Scenario testing:** Explore multiple scenarios (e.g. different budgets or mandatory allocations) to understand trade‑offs.  The flexibility of the optimization formulation makes it straightforward to try alternative assumptions.

## Deployment Options

- **Local execution:** Run the notebooks or scripts directly from your local environment.  This mode is ideal for analysts comfortable with Python and Jupyter.
- **Streamlit app:** Build an interactive dashboard for stakeholders.  Streamlit allows you to build a web UI with simple Python code; provide controls for budget, filters and display charts of the results.
- **Command‑line interface (CLI):** Implement an entry point using `argparse` to allow running the optimizer from the command line with parameters (budget, input file, etc.).
- **Docker container:** For consistent deployment, create a `Dockerfile` that installs dependencies and runs the optimizer.  Users can then run the container on any platform without worrying about environment configuration.  See the project wiki or docs folder for a sample Docker setup (to be added).

## Repository Structure

- `optimizer.py` &mdash; initial prototype script for defining and solving the allocation problem; this will be modularized into separate modules under `src/` as the project matures.
- `requirements.txt` &mdash; lists core and optional Python dependencies.
- `README.md` &mdash; project documentation (this file).
- `data/` &mdash; (not included) place your input datasets here.
- `notebooks/` &mdash; (optional) notebooks for exploratory analysis and model training.
- `src/` &mdash; (optional) Python modules for data ingestion, processing, modeling and optimization.
- `reports/` &mdash; (optional) folder for storing generated reports or results.

## Notes

- The default solver via PuLP uses the open‑source CBC backend, which is sufficient for moderate problem sizes.  If you need to scale to larger instances, consider installing a commercial solver and configuring PuLP or switching to Pyomo/OR‑Tools.
- To customize the objective or add constraints (e.g. sector quotas, geographic fairness), edit the optimization script.  Linear and integer constraints can be added easily in PuLP.
- No raw datasets or sensitive information should be committed to this repository.  Provide sample or simulated data for demonstration purposes.

---

This optimizer is intended to help decision makers allocate social impact funds more effectively by combining data science and operations research.  Feedback and contributions are welcome.
