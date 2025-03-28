# Course topics

## Week 1: Introduction, Hopfield Networks
  - Discussions:
    - What is memory?
    - What does it mean to build a "model" of memory?
    - Are neural networks like biological brains?
  - Hebbian learning and Hopfield networks
    - Reading: [Hopfield (1982)](https://www.pnas.org/doi/abs/10.1073/pnas.79.8.2554)
  - Discussion: Hopfield network simulations (storage capacity, cued recall, contextual drift)
  - **Assignment 1**: [Explore Hopfield Networks](https://github.com/ContextLab/memory-models-course/tree/main/assignments/Assignment%201%3A%20Hopfield%20Networks)


## Weeks 2--3: Free recall, Short Term and Long Term Memory
  - Discussions:
    - free recall and memory search
    - naturalistic memory tasks
  - Readings:
    - [Atkinson and Shiffrin (1968)](https://www.dropbox.com/scl/fi/rpllozjcv704okckjdy5k/AtkiShif68.pdf?rlkey=i0azhj9mqxws7bxocbl65j88d&dl=1)
    - [Chen et al. (2016)](https://www.dropbox.com/scl/fi/wg6fledn7g88ig5mk3kob/ChenEtal16.pdf?rlkey=9jqu7y2apqv2hrj8qepn4alwa&dl=1)
    - [Heusser et al. (2021)](https://www.dropbox.com/scl/fi/w7z2yvdfzmhowh5hvg53e/HeusEtal21.pdf?rlkey=omad9klqeiu2kc71w7guc5xxq&dl=1)
  - Data science primer:
    - Where to find behavioral datasets: [Penn Behavioral Data Archive](https://memory.psych.upenn.edu/Data_Archive), [OpenCogData](https://nimh-dsst.github.io/OpenCogData/), [OpenNeuro](https://openneuro.org/), [UCLA Psychological Dataset Archive](https://guides.library.ucla.edu/psychology/data), [Context Lab](https://www.context-lab.com/publications)
    - Web scraping with [requests](https://pypi.org/project/requests/) and [Beautiful Soup](https://beautiful-soup-4.readthedocs.io/en/latest/)
    - Data manipulation with [Pandas](https://pandas.pydata.org/)
    - Text analyses with [Scikit-learn](https://scikit-learn.org), [NLTK](https://www.nltk.org/), and [HuggingFace Transformers](https://huggingface.co/docs/transformers/en/index)
  - **Assignment 2**: [Build the Search of Associative Memory Model](https://github.com/ContextLab/memory-models-course/tree/main/assignments/Assignment%202%3A%20Search%20of%20Associative%20Memory%20Model)


## Weeks 4--5: Temporal Context and Multi-Timescale Models
- Discussion: the temporal scales of memory, event boundaries, and situation models
- Readings:
  - [Howard and Kahana (2002)](https://www.dropbox.com/scl/fi/yjnusbmoixbf4aen1mkx8/HowaKaha02.pdf?rlkey=ktt245cw09szubjnoe4cco1tz&dl=1)
  - [Polyn et al. (2009)](https://www.dropbox.com/scl/fi/98pui63j3o62xu96ciwhy/PolyEtal09.pdf?rlkey=42sc17ll573sm83g4q8q9x9nq&dl=1)
  - [Baldassano et al. (2017)](https://www.dropbox.com/scl/fi/wgn96xni9fevoo6h1yngn/BaldEtal17.pdf?rlkey=wg9qugm1szfw50xao6k9047j6&dl=1)
  - [Honey et al. (2012)](https://www.dropbox.com/scl/fi/l3vzzc56jjhq9tc4cheev/HoneEtal12.pdf?rlkey=56wf835omj2i6gkdh0b8n38cx&dl=1)
  - [Manning et al. (2014)](https://www.dropbox.com/scl/fi/a1zltxk43dn8qmm7puaql/MannEtal14d.pdf?rlkey=wg2ikym1svvl68hbuw4f5cpax&dl=1)
  - [Ranganath and Ritchey (2012)](https://www.dropbox.com/scl/fi/asec4p68900eekp6vtdgb/RangRitc12.pdf?rlkey=hqixac8eij65hmn62stzvo4mp&dl=1)
  - [DuBrow and Davachi (2016)](https://www.dropbox.com/scl/fi/86gkrz0a9k57556tz4d2z/DuBrDava16.pdf?rlkey=v6hxkbzz80m48pz4a2425q6bn&dl=1)
  - [Zacks and Tversky (2001)](https://www.dropbox.com/scl/fi/28104fmu9kzk55znyxntd/ZackTver01.pdf?rlkey=2ytdz0e9agny4hmllcw7hvi8g&dl=1)
  - [Zwann and Radvansky (1998)](https://www.dropbox.com/scl/fi/iqp70crdmpd5m97zzv45c/ZwaaRadv98.pdf?rlkey=habx93aplwkkw829vj9vkv52a&dl=1)
  - [Brunec et al. (2018)](https://www.dropbox.com/scl/fi/1eu28rpwyp8eg2sn4fgau/BrunEtal18b.pdf?rlkey=64dnn3onc90o59fuv33peil6g&dl=1)
- **Assignemnt 3**: [Build the Context Maintenance and Retrieval Model](https://github.com/ContextLab/memory-models-course/tree/main/assignments/Assignment%203%3A%20Context%20Maintenance%20and%20Retrieval%20Model)

## Week 6--7: Laplace Transforms
- Discussion: is TCM *really* multi-timescale?
- Discussion: Introduction to the Laplace Transform (and its inverse) and its relevance to memory
- Readings:
  - [Shankar and Howard (2012)](https://www.dropbox.com/scl/fi/cqh37rsdn11f6egdiskvf/ShanHowa12.pdf?rlkey=45qhdi5u2fmlxd4azq8is3j89&dl=1)
  - [Manning (2024)](https://www.dropbox.com/scl/fi/9amk5mlgeop0srtpwqesg/Mann23.pdf?rlkey=lc785xhq1pcjqdtarn692e21k&dl=1)
- **Assignment 4**: [Implement the Laplace Temporal Context Model](https://github.com/ContextLab/memory-models-course/tree/main/assignments/Assignment%204%3Laplace%20Temporal%20Context%20Model)

## Week 8: Biologically Inspired Network Models
- Discussion: what does "biologically inspired" mean in practice?
- Readings:
  - [McClelland et al. (1995)](https://imss-www.upmf-grenoble.fr/prevert/MasterICA/SpecialiteSC/FichiersPDF/Why%20there%20are%20complementary%20learning%20systems%20in%20the%20hippocampus%20and%20neocortex%20insights%20from%20th.pdf)
  - [Rumelhart et al. (1986)](http://www.cs.toronto.edu/~fritz/absps/pdp2.pdf)
  - [O'Reilly and Norman (2002)](http://www.princeton.edu/~compmem/normorei02.pdf)
  - [Schapiro et al. (2017)](https://www.dropbox.com/scl/fi/no2647c2witr2knb76gs2/SchaEtal17.pdf?rlkey=bpon63fy8g2rl3y9csabq748o&dl=1)

## Week 9: Recurrent networks, LSTM networks, Transformers
- Readings:
  - [Schuster and Paliwal (1997)](https://www.dropbox.com/scl/fi/0guahq2kcbria108xyb9j/SchuPali97.pdf?rlkey=yp1a8272qhljeob68amdpxjki&dl=1)
  - [Hochreiter and Schmidhuber (1997)](https://deeplearning.cs.cmu.edu/S23/document/readings/LSTM.pdf)
  - [Radford et al. (2019)](https://insightcivic.s3.us-east-1.amazonaws.com/language-models.pdf)
    - Tutorial video: [Let's build GPT: from scratch, in code, spelled out](https://www.youtube.com/watch?v=kCc8FmEb1nY)
- **Assignment 5**: [Final Project](https://github.com/ContextLab/memory-models-course/tree/main/assignments/Final%20Project)

## Week 10: Final project presentations
- Discussion: ad-hoc discussions and demos of final projects
- **Final projects are due on the last day of class at 11:59PM Eastern Time**