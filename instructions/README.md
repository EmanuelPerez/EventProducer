Produce samples locally with EventProducer
=============


```
git clone git@github.com:HEP-FCC/EventProducer.git
cd EventProducer/
git checkout spring2021
```

To-do's  for local running :
========================

  - In your own /eos directory (or in a subdirectory of your /eos directory), create a subdirectory "generation", and, inside "generation", create a subdirectory "stdhep" and a subdirectory "DelphesEvents". The "generation" directory is labeled below as /YOUR\_EOS\_DIRECTORY/generation. Example:
``` 
mkdir /eos/user/e/eperez/generation
mkdir /eos/user/e/eperez/generation/stdhep
mkdir /eos/user/e/eperez/generation/DelphesEvents
```

  - in your /afs directory, create the following directories:
    - /afs/cern.ch/user/U/USERID/www/data/
    - /afs/cern.ch/user/U/USERID/public/FCCDicts/yaml/FCCee

  - Edit the file param\_FCCee.py  in the config directory: 
    - Replace everywhere helsens by your userid
    - Search for stdhep\_dir and replace /eos/experiment/fcc/ee/generation/stdhep/ by   /YOUR\_EOS\_DIRECTORY/generation/stdhep/
    - Search for delphes\_dir and replace /eos/experiment/fcc/ee/generation/DelphesEvents/ by  /YOUR\_EOS\_DIRECTORY/generation/DelphesEvents




Instructions to produce a sample that is already defined in param\_FCCee.py, but for "spring2021_training" :
==========================================

Example for the wzp6\_ee\_mumuH\_ecm240 dataset 

### Setup 
from the EventProducer top directory :
```
source ./init.sh
```


### Create the stdhep files 

  - first, test in local that things run fine :
```
python bin/run.py --FCCee --STDHEP --send -p wzp6_ee_mumuH_ecm240 --typestdhep wzp6 -N 1 -n 100 --version spring2021_training --local
```
This must createa jobXXXX\_wzp6\_ee\_mumuH\_ecm240 directory, with  some whizard files in. It  also creates a directory BatchOutputs/FCCee/stdhep/wzp6\_ee\_mumuH\_ecm240, which contains the script that is run, and the gzip'ed stdhep event file. You can delete these directories later.
 
  - if no error, submit the jobs : below, 10 jobs are submitted, each producing 100k events. You will have to change group\_u\_CMST3.all by a group that you can use (or use the default, i.e. remove the --priority group ):
```
python bin/run.py --FCCee --STDHEP --send -p wzp6_ee_mumuH_ecm240  --typestdhep wzp6 -N 10 -n 100000 --version spring2021_training --condor -q longlunch --priority group_u_CMST3.all
```
  Once the jobs  are done, you should see your  gzipp'ed stdhep files in your  /eos directory, and some new files created in BatchOutputs/FCCee/stdhep/wzp6\_ee\_mumuH\_ecm240.

  - Once  the condor jobs  are done: check the EOS files and update the yamls: (the second command takes some time, that's normal) :
```
python bin/run.py --FCCee --STDHEP --checkeos -p wzp6_ee_mumuH_ecm240 --version spring2021_training
python bin/run.py --FCCee --STDHEP --check  -p wzp6_ee_mumuH_ecm240 --version spring2021_training
python bin/run.py --FCCee --STDHEP --merge -p wzp6_ee_mumuH_ecm240 --version spring2021_training
```
 You should see some files created in /afs/cern.ch/work/e/eperez/public/FCCDicts/yaml/FCCee/stdhep/wzp6\_ee\_mumuH\_ecm240, in particular files events\_xxx.yaml and a file merge.yaml.

### Run Delphes

Finally, one can run Delphes :
   - first check in local :
```
python bin/run.py --FCCee --reco --send --version spring2021_training --detector IDEA  -p wzp6_ee_mumuH_ecm240 --type stdhep  -N 1 --local
```
 You should get no error and see files crates in BatchOutputs/FCCee/spring2021\_training/IDEA/wzp6\_ee\_mumuH\_ecm240/ (an event\_XXX.root file, and the script that was run, jobXXX.sh)

  - and then submit all jobs on condor 
```
python bin/run.py --FCCee --reco --send --version spring2021_training --detector IDEA  -p wzp6_ee_mumuH_ecm240 --type stdhep  -N 10  --condor -q longlunch --priority group_u_CMST3.all
```
Once the jobs are done, you should see your samples in your /eos directory, in generation/DelphesEvents/spring2021\_training/IDEA/wzp6\_ee\_mumuH\_ecm240/, and some files in BatchOutputs/FCCee/spring2021_training/IDEA/wzp6\_ee\_mumuH\_ecm240/. You will have large .error files because Delphes prints a message each time an event is produced... If everything went  well, delete these files, otherwise you will quickly explode your afs quota.

  - and update the yamls for the Delphes files:
```
python bin/run.py --FCCee --reco --checkeos -p wzp6_ee_mumuH_ecm240 --version spring2021_training --detector IDEA
python bin/run.py --FCCee --reco --check -p wzp6_ee_mumuH_ecm240 --version spring2021_training --detector IDEA
python bin/run.py --FCCee --reco --merge -p wzp6_ee_mumuH_ecm240 --version spring2021_training --detector IDEA
```
  This creates the yaml files in your public/FCCDicts/yaml/FCCee/spring2021\_training/IDEA/wzp6\_ee\_mumuH\_ecm240

  - Finaly, create files for the web page and the json directory :
```
python bin/run.py --FCCee --reco --web --version spring2021_training --detector IDEA
python bin/run.py --FCCee --reco --sample --version spring2021_training --detector IDEA
```
You should then have a file FCCee\_procDict\_spring2021\_training\_IDEA.json  in your public/FCCDicts/. When you run FCCAnalyses, you  can change the path for the json files in config/common\_defaults.py, in order  to use your own json files, for your spring2021\_training samples.



