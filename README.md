_NOTE_: использовал chart [bitnami/rabbitmq](https://github.com/bitnami/charts/tree/master/bitnami/rabbitmq)

Основными различиями конфигураций HA в rabbitmq является CAP tradeoff. В данном случае я выбрал availabiltiy > consistency.
Параметры которые на это влияют:
- `policy.ha-sync-mode: manual`
  Так как синхронизация `queue mirror` это блокирующая операция, нам нужно сократить ее по максимуму. Поэтому при создании нового `mirror` запрещаем синхронизацию уже имеющихся сообщений, что может привести к `data loss` если `master` упадет. availability > consistency 
https://www.rabbitmq.com/ha.html#configuring-synchronisation
- `policy.ha-promote-on-failure/shutdown: always`
Если `master` упал, переносим его на `mirror`, даже если он полностью не синхронизован. availabiltiy > consistency
https://www.rabbitmq.com/ha.html#promoting-unsynchronised-mirrors
- `cluster_partition_handling: autoheal`
Во время `network partition` возможен `split-brain`. После `network partition` будет выбрана самая свежая реплика, все остальные зарезетятся. data loss. availability > consistency
https://www.rabbitmq.com/partitions.html#automatic-handling
