
First step: create parent dag:
```
p_dag = DAG(
    dag_id='pdag',
    default_args=args,
    schedule_interval="* */1 * * *",
    start_date=days_ago(1),
    dagrun_timeout=timedelta(minutes=1))
    
```

Second create method to call subdags and tasks

```
def sub_dag(pdag,cdag,date1,sch):

    c_dag = DAG(
        dag_id='%s.%s' %(pdag,cdag),
        default_args=args,
        schedule_interval="* */1 * * *",
    )
    dummy_op = DummyOperator(
            task_id='four', dag=c_dag,
        )
    final = DummyOperator(
        task_id='final', dag=c_dag,
    )

    for x in range(5):
        t1 = DummyOperator(
            task_id='task_'+str(x), dag=c_dag,
        )
        dummy_op >> t1 >> final



    return c_dag

```

third is to call the subdag from parent dag:
```
sub_dag = SubDagOperator(
    task_id='subdag_op',
    subdag=sub_dag('pdag','subdag_op',p_dag.start_date,p_dag.schedule_interval),
    default_args=args,

    dag=p_dag,
)

```
the format should be parentdag.subdag
