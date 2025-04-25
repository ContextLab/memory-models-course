This archive contains a MATLAB-based data structure containing the behavioral data 
from the experiment described in the article:

A Context Maintenance and Retrieval Model of Organizational Processes in Free Recall

Sean M. Polyn, Kenneth A. Norman, and Michael J. Kahana

Psychological Review, Vol. 116 (1), 129-156.

Refer to this manuscript for the methods of the experiment, and description of the 
analyses that we carried out on these data.

%%%%%%%%%%%%%%%%%%%

This particular file written by Sean Polyn
sean.polyn@vanderbilt.edu
on September 25th, 2013

Send word if you find anything weird or out of sorts with the data, or the explanation of the organization of the data!

If you are interested in the Context Maintenance and Retrieval model of human memory, go to this webpage:
http://memory.psy.vanderbilt.edu/groups/vcml/wiki/618f3/CMR_Documentation.html

Behavioral Toolbox (Release 1) analysis code available from:
http://memory.psych.upenn.edu/behavioral_toolbox

%%%%%%%%%%%%%%%%%%%

A quick tour of the data structure.

%%%%%%%%%%%%%%%%%%%

If you load the file PolyEtal09_data.mat in MATLAB, you will find a structure with three fields:

data.full   	% Data from all of the trials from the experiment, from all conditions, including practice trials
data.co		% Just the control trials, in which all items were studied using the same encoding task
data.sh		% Just the task-shift trials, in which participants shifted back and forth between the two encoding tasks

The organization of the sub-fields:

There are a number of sub-fields on the data structure.  Each row corresponds to a particular trial.  If there is more than one column, then there are two possible organizations, refer below to see which one applies.  (1) Yoked to the presentation order, each column corresponds to a study event.  (2) Yoked to the recall order, each column corresponds to a recall event.

The most critical sub-fields:

data.subject		% Each row has a numerical index, a unique subject identifier.  There are 45 unique subject identifiers.  The careful 
			        % observer will note that index 19 is skipped, this participant did not complete the study. 
data.listType		% 0 = all items studied using the SIZE task, 1 = all items studied using ANIMACY task, 2 = task-shift list
data.recalls		% A numerical identifier for each response made by the participant during the free recall period.  Integers 1-24   
			    	% correspond to the serial position of the recalled item.  Yoked to the recall order.  -1 corresponds to an intrusion.  
				% -2 corresponds to a repetition.
data.pres_task		% Which task was associated with each studied item, columns yoked to presentation events.  
			  	% Task 0 is SIZE
				% Task 1 is ANIMACY 
data.listLength	% There were 24 items on each study list

The other sub-fields:

data.session		% A session label for each trial, either 1 or 2
data.pres_itemnos	% Each studied item has an index for the wordpool.  Yoked to presentation order.
data.react_time	% Yoked to the study period.  Time to make the task response in milliseconds.
data.intrusions	% -1 for extra-experimental intrusion, positive numbers correspond to how many lists back a prior-list intrusion 
		     	        % came from. 
data.times		% For each recall response, how many milliseconds after the onset of the recall period was this response made.

Convenience fields (technically these are redundant with information in the other fields):

data.task   	   		% The task label of each recalled item (can be constructed with pres_task and recalls)
data.rec_itemnos  		% The wordpool index for each recalled item (can be constructed with pres_itemnos and recalls)
data.pres_subrec		% Yoked to presentation order.  1 if the item will be recalled.
data.pres_trainno		% Yoked to presentation order.  Labeling each item as to whether it is in the first train, second train, etc.
data.pres_trainlen		% Yoked to presentation order.  How long is the train that the item resides in.
data.pres_sertrain		% Yoked to presentation order.  Serial position of the item within a given train.
data.train			  	% Yoked to recall order, as above.
data.trainlen			% Yoked to recall order, as above.
data.sertrain			% Yoked to recall order, as above.

%%%%%%%%%%%%%%%%%%%

Other files that are included.

%%%%%%%%%%%%%%%%%%%

tfr_wp			% This is the wordpool for the experiment.  The index values in pres_itemnos and rec_itemnos can be used to 
			        % figure out which words are presented on each trial.  

sem_mat			% These are the LSA values used for the semantic analyses described 
