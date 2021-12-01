# sreport

```
$ sreport cluster <REPORT> <OPTIONS>
$ sreport job <REPORT> <OPTIONS>
$ sreport reservation Utilization <OPTIONS>
$ sreport user TopUsage <OPTIONS>
```

[https://github.com/OleHolmNielsen/Slurm_tools](https://github.com/OleHolmNielsen/Slurm_tools)

```
$ sreport cluster AccountUtilizationByUser tree start=2021-01-01 end=2021-11-28
$ sreport cluster AccountUtilizationByUser tree | grep -E ' {35}'
$ sreport cluster AccountUtilizationByUser tree -t percent  | grep -E ' {35}'
```

Seems stupid that root does not equal 100%:

```
$ sreport cluster AccountUtilizationByUser tree -t percent  | grep -E ' {35}'
    della root                                              42.72%    0.00% 
    della  astro                                             0.98%    0.00% 
    della  cbe                                               0.48%    0.00% 
    della  cee                                               1.31%    0.00% 
    della  chem                                             13.84%    0.00% 
    della  cses                                              0.01%    0.00% 
    della  ee                                                6.26%    0.00% 
    della  eeb                                               1.02%    0.00% 
    della  geo                                               0.66%    0.00% 
    della   geoclim                                          0.65%    0.00% 
    della  jenkins                                           1.00%    0.00% 
    della  mae                                               0.17%    0.00% 
    della  molbio                                            1.20%    0.00% 
    della  physics                                           4.06%    0.00% 
    della  pni                                               0.94%    0.00% 
    della  politics                                          0.03%    0.00% 
    della  prism                                             3.17%    0.00% 
    della  public                                            5.84%    0.00% 
    della   cs                                               1.41%    0.00% 
    della   econ                                             0.58%    0.00% 
    della   genomics                                         1.22%    0.00% 
    della   orfe                                             0.02%    0.00% 
    della   pcts                                             2.44%    0.00% 
    della   psychology                                       0.03%    0.00% 
    della   socio                                            0.15%    0.00% 
    della  spia                                              1.74%    0.00%
```

Why does the sum of the columns not equal root? Need to eliminate subgroups.

```
$ sreport cluster AccountUtilizationByUser tree  | grep -E ' {35}'
    della root                                             7450680        0 
    della  astro                                            170889        0 
    della  cbe                                               84124        0 
    della  cee                                              228852        0 
    della  chem                                            2414029        0 
    della  cses                                               1524        0 
    della  ee                                              1091272        0 
    della  eeb                                              178398        0 
    della  geo                                              114666        0 
    della   geoclim                                         112828        0 
    della  jenkins                                          174559        0 
    della  mae                                               30025        0 
    della  molbio                                           209754        0 
    della  physics                                          708666        0 
    della  pni                                              164530        0 
    della  politics                                           4844        0 
    della  prism                                            552916        0 
    della  public                                          1018514        0 
    della   cs                                              245224        0 
    della   econ                                            101835        0 
    della   genomics                                        211952        0 
    della   orfe                                              2805        0 
    della   pcts                                            426091        0 
    della   psychology                                        4565        0 
    della   socio                                            26042        0 
    della  spia                                             303119        0 

$ sreport cluster AccountUtilizationByUser tree | grep -E ' {35}' | grep -v root | awk -F' ' '{sum+=$3;} END{print sum;}'
8582023

$ sreport cluster AccountUtilizationByUser tree | grep -E ' {35}' | grep -v -E 'root|geoclim| cs |econ|genom|orfe|pcts|psych|socio' | awk -F' ' '{sum+=$3;} END{print sum;}'
7450681
```

```
$ sreport -t hourper cluster Utilization Start=`date -d "last month" +%D` End=`date -d "this month" +%D`
--------------------------------------------------------------------------------
Cluster Utilization 2021-09-01T00:00:00 - 2021-09-30T23:59:59
Usage reported in CPU Hours/Percentage of Total
--------------------------------------------------------------------------------
  Cluster            Allocated               Down         PLND Down                Idle             Reserved             Reported 
--------- -------------------- ------------------ ----------------- ------------------- -------------------- -------------------- 
    della      3536774(40.56%)      140182(1.61%)          0(0.00%)     1013627(11.62%)      4030057(46.21%)     8720640(100.00%) 
```


```
$ sreport -v cluster AccountUtilizationByUser cluster=stellar accounts=cimes start=2021-01-01 end=now -t hours
```

```
$ sreport user top start=2021-10-01 end=now TopCount=100 accounts=pppl -t hourper --tres=cpu
$ sreport user top start=2021-10-01 end=now TopCount=100 -t hourper --tres=cpu
$ sreport cluster AccountUtilizationByUser cluster=della user=yaojunz start=8/31/20 end=8/31/21 -t hours
$ sreport cluster AccountUtilizationByUser cluster=traverse start=2021-10-01 end=now -t hours
 ...
 traverse           tromp                                 92667        0 
 traverse           tromp   lsawade    Lucas Sawade          80        0 
 traverse           tromp       ql5   Qiancheng Liu         329        0 
```
Why doesn't lsawade and ql5 not sum to 92667?

```
$ sreport user top start=2020-11-01 end=now TopCount=10000 -t hourper --tres=cpu | grep dmturner
   tiger2  dmturner    David Turner             pni            cpu           111(0.00%) 
   tiger2  dmturner    David Turner            cses            cpu            46(0.00%) 
```

Start with `sreport user top start=2020-11-01 end=now TopCount=10000 -t hourper --tres=cpu` to get netid, slurm account and usage. This command is best because it covers cases where user's have multiple slurm accounts. Generating the netids this way is better than ls -l /home or getent passwd because it remembers the netid even if the user is removed from the cluster.

Use getent passwd to get the sponsor(s). Use checkgpu for utilization and gpu-hours. Check gpu-hours against sacct.

Use ldapsearch on nobel to get position.

```
$ sreport user topusage start=2021-10-01 end=now TopCount=10000 format=Login,Account,Proper,Used -t hours -P -n
```

Number of jobs:

```
$ sreport job sizesbyaccount users=smajeski start=2021-11-23T00:00:00 end=2021-11-29T23:59:59 PrintJobCount
$ sacct -u smajeski -S 2021-11-23T00:00:00 -E 2021-11-29T23:59:59 -X -n | wc -l
```
