# Femto Dream

Codes: `O2Physics/PWGCF/FemtoDream/`

## Tutorials and slides
- [PWGCF hands-on workshop 22.11.2021](https://indico.cern.ch/event/1092615/), especially:
  - [introduction to FemtoDream](https://indico.cern.ch/event/1092615/contributions/4594901/attachments/2350467/4009563/tutorial.pdf)
  - [tutorial for pp correlations](https://indico.cern.ch/event/1092615/contributions/4594904/attachments/2350474/4009036/Obtaining%20p-p%20correlations%20in%20o2.pdf)
- [FemtoDream report at the WP4 + WP14 meeting 19.07.2021](https://indico.cern.ch/event/1060355/contributions/4455868/attachments/2283669/3881071/WP14_FemtoDream_190721.pdf)

## Produce the data:
```bash
o2-analysis-timestamp --aod-file AO2D.root -b |
  o2-analysis-multiplicity-table -b |
  o2-analysis-event-selection --syst pp -b |
  o2-analysis-trackextension -b |
  o2-analysis-pid-tpc -b | o2-analysis-pid-tof -b |
  o2-analysis-weak-decay-indices -b |
  o2-analysis-lf-lambdakzerobuilder -b |
  o2-analysis-cf-femtodream-producer --aod-writer-keep AOD/FEMTODREAMPARTS/0,AOD/FEMTODREAMCOLS/0 --aod-writer-resfile FemtoAO2D -b --aod-memory-rate-limit 600000000
```

## Launch a sample task:
```bash
o2-analysis-cf-femtodream-hash -b | o2-analysis-cf-femtodream-pair-track-track --aod-file FemtoAO2D.root --aod-memory-rate-limit 600000000 --ConfCutPartTwo 5543046 --ConfCutPartOne 5543046
```

Note:
- the hash task should disappear, there will be soon a better interface
- aod-memory-rate-limit is probably no longer needed

## Meeting with Valentina August 2021:
- they have track-track for now
- some info missing in their table for angcorr, so we need to adjust (momentum, dca, ...)
- the main core the same, but the analysis different
- lambda-p: if L decays into p, we reject a pair of p daughters with mother lambda
BUT: There can be 2 lambdas next to each other, which decay into protons. We choose the one with bigger dcaXY (pt?), but not the second one (so as not to correlate the protons that are too close to each other?)
- femto table: tracks, v0, cascades, one group after another one
- use indices int[2] to remove autocorrelations
- pidcut: not fully implemented now, in the future they want to have same task for different particles
- indices are not really used in the producer, just to find the daughters
- at the moment some configurables in the cut container are fixed, to be extended (O2 framework limits)
- femtoDreamPairTaskTrackTrack.cxx - example analysis
- they want to have conf PID on bits instead of ints
- cutContainer - related to particle kind selection, would be nice to have PID here (systematic variation)
