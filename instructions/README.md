Produce samples locally with EventProducer
=============


```
git clone git@github.com:HEP-FCC/EventProducer.git
git checkout spring2021
```

To-do's  for local running :
========================

  - In your own /eos directory (or in a subdirectory of your. /eos directory), create a subdirectory "generation", and, inside "generation", create a subdirectory "stdhep" and a subdirectory "DelphesEvents". The "generation" directory is labeled below as /YOUR\_EOS\_DIRECTORY/generation

  - in your /afs directory, create the following directories:
    - /afs/cern.ch/user/U/USERID/www/data/
    - /afs/cern.ch/user/U/USERID/public/FCCDicts/yaml/FCCee

  - Edit the file param\_FCCee.py  in the config directory: 
    - Replace everywhere helsens by your userid
    - Search for stdhep\_dir and replace /eos/experiment/fcc/ee/generation/stdhep/ by   /YOUR\_EOS\_DIRECTORY/generation/stdhep/
    - Search for delphes\_dir and replace /eos/experiment/fcc/ee/generation/DelphesEvents/ by  /YOUR\_EOS\_DIRECTORY/generation/DelphesEvents




Instructions to produce a sample that is already defined in param\_FCCee.py, but for "training" :
==========================================

Example for the wzp6\_ee\_mumuH\_ecm240 dataset 

  - Create the stdhep files :
    - first, test in local that things run fine :
```
python bin/run.py --FCCee --STDHEP --send -p wzp6_ee_mumuH_ecm240 -N 1 -n 100 --version spring2021_training --local
```
    - if no error, submit the jobs : below, 10 jobs are submitted, each producing 100k events. You will have to change group\_u\_CMST3.all by a group that you can use (or use the default, i.e. remove the --priority group ):
```
python bin/run.py --FCCee --STDHEP --send -p wzp6_ee_mumuH_ecm240  --typestdhep wzp6 -N 10 -n 100000 --version spring2021_training --condor -q longlunch --priority group_u_CMST3.all
```

  - Once  the condor jobs  are done: check the EOS files and update the yamls: (the second command takes some time, that's normal) :
```
python bin/run.py --FCCee --STDHEP --checkeos -p wzp6_ee_mumuH_ecm240 --version spring2021_training
python bin/run.py --FCCee --STDHEP --check  -p wzp6_ee_mumuH_ecm240 --version spring2021_training
python bin/run.py --FCCee --STDHEP --merge -p wzp6_ee_mumuH_ecm240 --version spring2021_training
```

  - Finally, one can run Delphes :
```
python bin/run.py --FCCee --reco --send --version spring2021_training --detector IDEA  -p wzp6_ee_mumuH_ecm240 --type stdhep  -N 10  --condor -q longlunch --priority group_u_CMST3.all
```

  - and update the yamls for the Delphes files:
```
python bin/run.py --FCCee --reco --checkeos -p wzp6_ee_mumuH_ecm240 --version spring2021_training --detector IDEA
python bin/run.py --FCCee --reco --check -p wzp6_ee_mumuH_ecm240 --version spring2021_training --detector IDEA
python bin/run.py --FCCee --reco --merge -p wzp6_ee_mumuH_ecm240 --version spring2021_training --detector IDEA
```


