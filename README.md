# sreport_cluster_usage

```
$ sreport cluster AccountUtilizationByUser tree
$ sreport cluster AccountUtilizationByUser tree | grep -E ' {35}'
$ sreport cluster AccountUtilizationByUser tree -t percent  | grep -E ' {35}'
```

What does the sum of the columns not equal root?

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
```
