# Praxis Interface

<p align="center">
<a href="https://github.com/praxis-pioneering/client/actions"><img alt="Actions Status" src="https://github.com/psf/black/workflows/Test/badge.svg"></a>
<a href="https://github.com/psf/black"><img alt="Code style: black" src="https://img.shields.io/badge/code%20style-black-000000.svg"></a>
<a href="https://www.python.org/"><img alt="Python 3.7/3.8/3.9" src="https://img.shields.io/badge/python-3.7%20%7C%203.8%20%7C%203.9-blue"></a>
</p>

Praxis Interface is a simple tool that helps teams reason about data that evolves over time. It is used internally by the Praxis team for financial forecasts of over $30B in transactional volume.

![overview](https://drive.google.com/uc?id=1wUv8d8Qvdrfo1SVawsvAkqsMAfZI0Vdu)

## Features

- Visualize different metrics and time-series IDs and their decompositions from your pandas DataFrame
- Join covariates to your target series
- Find leading indicators among your covariates
- Export train-test splits given timestamp
- Quickly train baseline statistical and ML models (single-series and multi-series), export predictions, residuals, and model
- Benchmark different feature engineering settings and models

## Getting Started

To install the Praxis interface, simply download the pip wheel from the URL and launch a Jupyter Lab/Notebook environment:

```python
!pip install https://storage.googleapis.com/praxis-public/wheels/praxis_interface-0.0.9-py2.py3-none-any.whl
```

You could launch a Praxis interface in just a few lines:

```python
from praxis_interface import Praxis # import interface
import pandas as pd

praxis = Praxis(df) # instantiate Praxis interface
praxis.run(mode="inline") # running inline inside a cell
praxis.run(mode="external") # running in an extra tab

praxis.get_model() # retrieve baseline models
praxis.get_forecast() # retrive baseline forecasts
praxis.get_metrics() # retrieve benchmark results

train, test = praxis.to_dataframe() # export train/test splits
```

## How Does it Work?

For each interface, it mounts an in-process SQL OLAP (DuckDB) on top of your DataFrame and runs a Flask server on the same box that hosts your Jupyter instance. Each interaction with the interface eventually translates to a SQL query that runs on top of the database. Statistical and ML models are also trained with the CPU processing power of your box and stored within the Flask server, implemented partly by open-source libraries and partly by us.

<p style="text-align: center;"><b>To ensure that the DataFrame represents a time-series, right now we require it to include both a `id` and `date` column.</b>
</p>

## Reporting Bugs / Providing Feedbacks

The tool is still in alpha, so there may be rough edges and bugs. We'd love to hear from you for bug reports and feedbacks :)

Feel free to join our Community Slack channel at https://join.slack.com/t/praxiscommunity/shared_invite/zt-1ef4vfje9-VCdEThDKIrYd0Z5ErVGo9A, or alternatively send correspondences to engineering@praxispioneering.com.

## Relevant Links

- Colab demo: https://demo.praxispioneering.com

- Documentation and API reference: https://docs.praxispioneering.com

## Known Warnings / Recommendations

- After seeing:

  ```
  WARNING: The following packages were previously imported in this runtime:
    [pkg1, pkg2, ..]
  ```

  you may wanna restart the runtime, otherwise importing Praxis may not work.

- Pip may report an installation warning like:

  ```
  ERROR: pip's dependency resolver does not currently take into account all the packages that are installed.
  ```

  this won't have any affect on the success of installation. You may proceed as usual.

-  If you are encounter errors regarding `undefined symbol:` in `_XLAC` for `pytorch_lightning`, this may be due to a broken `torch_xla` installation. You may consider uninstalling it via `pip uninstall torch_xla`, then restart the runtime. The current Praxis interface does not take advantage of XLA yet.

- If you are in a hosted environment, the proxy set-up may also be buggy and results in the interface not showing up. Your browser may also be configured to not display the interface due to Enhanced Tracking Protection.

-  **To ensure that the DataFrame represents a time-series, right now we _require_ it to include both a `id` and `date` column.** All other columns will be treated as either a target series, or covariates.

  | **column name**      | **type**                  | **description**                                                                                      |
  | -------------------- | ------------------------- | ---------------------------------------------------------------------------------------------------- |
  | id                   | `str`                     | this defines a unique timeseries inside the dataframe. different timeseries must have different id's |
  | date                 | `datetime \| np.datetime` | time dimension of this timeseries                                                                    |
  | _{...other columns}_ | `non-null values`         | covariate and target values                                                                          |

  An example dataframe showing two websites -- alice.com and bob.com's view counts over time with some impacting covariates to explore:

  | **id**    | **date**   | **views** | **cov_1** | **cov_2** |
  | --------- | ---------- | --------- | --------- | --------- |
  | alice.com | 01-01-2020 | 100       | 628       | 1209      |
  | alice.com | 01-02-2020 | 200       | 362       | 105       |
  | alice.com | 01-03-2020 | 300       | 1802      | 87        |
  | bob.com   | 12-01-2018 | 4000      | 164       | 655       |
  | bob.com   | 12-02-2018 | 3500      | 720       | 1258      |
  | bob.com   | 12-02-2018 | 2400      | 1350      | 651       |
