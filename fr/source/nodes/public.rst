.. list-table::
   :header-rows: 1
   :width: 1280px
   :widths: 11 9 5 5 17 21 5 11 10

   * - Partition(s)
     - Nœud(s)
     - Cœurs
     - Mémoire
     - CPU
     - GPU
     - Stockage
     - Limite de temps (jours)
     - Propriétaire(s)
   * - ``iq-main``
     - ``cp370[2-4]``

       (total : 3)
     - 96
     - 500G
       
       (0,48T)
     - 2 x AMD EPYC 7643
       
       ``milan``
     -
     - 1T
       
       (NVMe)
     - 7
     - IQ
   * - ``iq-main``
     - ``cp3705``
     - 48
     - 500G
       
       (0,48T)
     - 2 x Intel Xeon Gold 6342
       
       ``icelake``
     - 2 x NVidia A40 48G
       
       ``nvidia_a40``
     - 1T
       
       (NVMe)
     - 7
     - IQ
