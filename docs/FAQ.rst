LightGBM FAQ
============

Contents
~~~~~~~~

-  `Critical <#critical>`__

-  `LightGBM <#lightgbm>`__

-  `R-package <#r-package>`__

-  `Python-package <#python-package>`__

--------------

Critical
~~~~~~~~

Please post an issue in `Microsoft/LightGBM repository <https://github.com/Microsoft/LightGBM/issues>`__ for any
LightGBM issues you encounter. For critical issues (crash, prediction error, nonsense outputs...), you may also ping a
member of the core team according the relevant area of expertise by mentioning them with the arobase (@) symbol:

-  `@guolinke <https://github.com/guolinke>`__ (C++ code / R-package / Python-package)
-  `@chivee <https://github.com/chivee>`__ (C++ code / Python-package)
-  `@Laurae2 <https://github.com/Laurae2>`__ (R-package)
-  `@wxchan <https://github.com/wxchan>`__ (Python-package)
-  `@henry0312 <https://github.com/henry0312>`__ (Python-package)
-  `@StrikerRUS <https://github.com/StrikerRUS>`__ (Python-package)
-  `@huanzhang12 <https://github.com/huanzhang12>`__ (GPU support)

Please include as much of the following information as possible when submitting a critical issue:

-  Is it reproducible on CLI (command line interface), R, and/or Python?

-  Is it specific to a wrapper? (R or Python?)

-  Is it specific to the compiler? (gcc versions? MinGW versions?)

-  Is it specific to your Operating System? (Windows? Linux?)

-  Are you able to reproduce this issue with a simple case?

-  Does the issue persist after removing all optimization flags and compiling LightGBM in debug mode?

When submitting issues, please keep in mind that this is largely a volunteer effort, and we may not be available 24/7 to provide support.

--------------

LightGBM
~~~~~~~~

-  **Question 1**: Where do I find more details about LightGBM parameters?

-  **Solution 1**: Take a look at `Parameters <./Parameters.rst>`__ and the `Laurae++/Parameters <https://sites.google.com/view/lauraepp/parameters>`__ website.

--------------

-  **Question 2**: On datasets with million of features, training does not start (or starts after a very long time).

-  **Solution 2**: Use a smaller value for ``bin_construct_sample_cnt`` and a larger value for ``min_data``.

--------------

-  **Question 3**: When running LightGBM on a large dataset, my computer runs out of RAM.

-  **Solution 3**: Multiple solutions: set the ``histogram_pool_size`` parameter to the MB you want to use for LightGBM (histogram\_pool\_size + dataset size = approximately RAM used),
   lower ``num_leaves`` or lower ``max_bin`` (see `Microsoft/LightGBM#562 <https://github.com/Microsoft/LightGBM/issues/562>`__).

--------------

-  **Question 4**: I am using Windows. Should I use Visual Studio or MinGW for compiling LightGBM?

-  **Solution 4**: Visual Studio `performs best for LightGBM <https://github.com/Microsoft/LightGBM/issues/542>`__.

--------------

-  **Question 5**: When using LightGBM GPU, I cannot reproduce results over several runs.

-  **Solution 5**: This is normal and expected behaviour, but you may try to use ``gpu_use_dp = true`` for reproducibility
   (see `Microsoft/LightGBM#560 <https://github.com/Microsoft/LightGBM/pull/560#issuecomment-304561654>`__).
   You may also use the CPU version.

--------------

-  **Question 6**: Bagging is not reproducible when changing the number of threads.

-  **Solution 6**: LightGBM bagging is multithreaded, so its output depends on the number of threads used.
   There is `no workaround currently <https://github.com/Microsoft/LightGBM/issues/632>`__.

--------------

-  **Question 7**: I tried to use Random Forest mode, and LightGBM crashes!

-  **Solution 7**: This is expected behaviour for arbitrary parameters. To enable Random Forest,
   you must use ``bagging_fraction`` and ``feature_fraction`` different from 1, along with a ``bagging_freq``.
   `This thread <https://github.com/Microsoft/LightGBM/issues/691>`__ includes an example.

--------------

-  **Question 8**: CPU usage is low (like 10%) in Windows when using LightGBM on very large datasets with many-core systems.

-  **Solution 8**: Please use `Visual Studio <https://visualstudio.microsoft.com/downloads/>`__
   as it may be `10x faster than MinGW <https://github.com/Microsoft/LightGBM/issues/749>`__ especially for very large trees.

--------------

-  **Question 9**: When I'm trying to specify a categorical column with the ``categorical_feature`` parameter,
   I get the following sequence of errors, but there are no negative values in the column.

   ::

       [LightGBM] [Warning] Met negative value in categorical features, will convert it to NaN
       [LightGBM] [Fatal] Cannot construct Dataset since there are no useful features.
       It should be at least two unique rows.
       If the num_row (num_data) is small, you can set min_data=1 and min_data_in_bin=1 to fix this.
       Otherwise, please make sure you are using the right dataset

-  **Solution 9**: The column you're trying to pass via ``categorical_feature`` likely contains very large values.
   Categorical features in LightGBM are limited by int32 range,
   so you cannot pass values that are greater than ``Int32.MaxValue`` (2147483647) as categorical features (see `Microsoft/LightGBM#1359 <https://github.com/Microsoft/LightGBM/issues/1359>`__).
   You should convert them to integers ranging from zero to the number of categories first.

--------------

R-package
~~~~~~~~~

-  **Question 1**: Any training command using LightGBM does not work after an error occurred during the training of a previous LightGBM model.

-  **Solution 1**: Run ``lgb.unloader(wipe = TRUE)`` in the R console, and recreate the LightGBM datasets (this will wipe all LightGBM-related variables).
   Due to the pointers, choosing to not wipe variables will not fix the error.
   This is a known issue: `Microsoft/LightGBM#698 <https://github.com/Microsoft/LightGBM/issues/698>`__.

--------------

-  **Question 2**: I used ``setinfo``, tried to print my ``lgb.Dataset``, and now the R console froze!

-  **Solution 2**: Avoid printing the ``lgb.Dataset`` after using ``setinfo``.
   This is a known bug: `Microsoft/LightGBM#539 <https://github.com/Microsoft/LightGBM/issues/539>`__.

--------------

Python-package
~~~~~~~~~~~~~~

-  **Question 1**: I see error messages like this when install from GitHub using ``python setup.py install``.

   ::

       error: Error: setup script specifies an absolute path:
       /Users/Microsoft/LightGBM/python-package/lightgbm/../../lib_lightgbm.so
       setup() arguments must *always* be /-separated paths relative to the setup.py directory, *never* absolute paths.

-  **Solution 1**: This error should be solved in latest version.
   If you still meet this error, try to remove ``lightgbm.egg-info`` folder in your Python-package and reinstall,
   or check `this thread on stackoverflow <http://stackoverflow.com/questions/18085571/pip-install-error-setup-script-specifies-an-absolute-path>`__.

--------------

-  **Question 2**: I see error messages like

   ::

       Cannot get/set label/weight/init_score/group/num_data/num_feature before construct dataset

   but I've already constructed a dataset by some code like

   ::

       train = lightgbm.Dataset(X_train, y_train)

   or error messages like

   ::

       Cannot set predictor/reference/categorical feature after freed raw data, set free_raw_data=False when construct Dataset to avoid this.

-  **Solution 2**: Because LightGBM constructs bin mappers to build trees, and train and valid Datasets within one Booster share the same bin mappers,
   categorical features and feature names etc., the Dataset objects are constructed when constructing a Booster.
   If you set ``free_raw_data=True`` (default), the raw data (with Python data struct) will be freed.
   So, if you want to:

   -  get label (or weight/init\_score/group) before constructing a dataset, it's same as get ``self.label``

   -  set label (or weight/init\_score/group) before constructing a dataset, it's same as ``self.label=some_label_array``

   -  get num\_data (or num\_feature) before constructing a dataset, you can get data with ``self.data``.
      Then, if your data is ``numpy.ndarray``, use some code like ``self.data.shape``

   -  set predictor (or reference/categorical feature) after constructing a dataset,
      you should set ``free_raw_data=False`` or init a Dataset object with the same raw data
