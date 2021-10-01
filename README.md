# sreport_cluster_usage

```
$ sreport cluster AccountUtilizationByUser tree
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

What does the sum of the columns not equal root? Need to eliminiate subgroups.

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
