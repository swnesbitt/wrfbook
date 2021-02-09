# Exercise 1: Creating an ensemble

For this exercise, we will use the `em_quarter_ss` simulation.

A best practice is to have your code base stored in a directory (in the projects directory, for example) and then copy your code base to a working directory (preferably in scratch space where disk access is high speed).  You can then archive those simulations (or just the namelist, input data, and output in a different directory).

You can use your batch script to manage these work flows.

Please run `ideal.exe` and `wrf.exe` and modify the namelist to write output (`history_interval`) every 5 minutes.

Create simulations that modify the `mp_physics` in `namelist.input` to run the model with options 1, 2, 6, 8, 10 and run .  Be sure that you save the output for each of these simulations (and their `namelist.input` files).