---
title: "How to use optuna-dashboard with pykeen"
date: 2023-12-04
permalink: /posts/2023/12/optuan-dashboard/
tags:
  - pykeen
  - optuna
  - optuna-dashboard
  - english
---

First install [optuna-dashboard](https://github.com/optuna/optuna-dashboard)

```
$ pip install optuna-dashboard
```

Then make sure there are any available [RDB](https://en.wikipedia.org/wiki/Relational_database)s.
Most linux systems come with [SQLite](https://www.sqlite.org/index.html).
So I'll use SQLite as an example.

we should add this parameter to _hpo_pipeline_

```
storage="sqlite:///your_path/{}.db".format("database_name"),
```

And using next command to see the results

```
cd your_path
optuna-dashboard sqlite:///database_name.db
```
