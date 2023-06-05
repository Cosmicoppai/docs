**Expected values for a healthy cluster**: The number of L0 files should **not** be in the high thousands. High values indicate heavy write load that is causing accumulation of files in level 0. These files are not being compacted quickly enough to lower levels, resulting in a misshapen LSM.