This is to trigger an external dag

1. create a dag with dag_id=triggerDagRun2 in first.py

```
from __future__ import absolute_import, unicode_literals
import os
from airflow.operators import BashOperator
from airflow.models import DAG
from airflow.operators import PythonOperator, DummyOperator,BashOperator,TriggerDagRunOperator
from datetime import datetime, timedelta




args2 = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2016, 3, 29, 8, 15),
}
dag2 = DAG(
    dag_id='triggerDagRun2',
    default_args=args2,
    schedule_interval=None,
    dagrun_timeout=timedelta(minutes=1),
)



def run_this_fun(**kwargs):
    print("Remotely received value of {} for key=message".
          format('shamim'))

one = PythonOperator(
            task_id='one',
            context=True,
            python_callable=run_this_fun,

            dag=dag2,
)
three = DummyOperator(task_id='three', dag=dag2)
four = DummyOperator(task_id='four', dag=dag2)
five = DummyOperator(task_id='five', dag=dag2)
six = DummyOperator(task_id='six', dag=dag2)
final = DummyOperator(task_id='final', dag=dag2)

one >> three >> four >> five >> six >> final
```

create another dag say triggerDagRun in second.py

```
args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2016, 3, 29, 8, 15),
}



dag = DAG(
    dag_id='triggerDagRun',
    default_args=args,
    schedule_interval="* */1 * * *",
    dagrun_timeout=timedelta(minutes=1))



def conditionally_trigger(context, dag_run_obj):
    dag_run_obj.payload={'message':'shamim'}
    return dag_run_obj

trigger = TriggerDagRunOperator(
        task_id='start-ssh-job',
        trigger_dag_id="triggerDagRun2",
        python_callable=conditionally_trigger,
        params={'message': 'Hello World'},
        provide_context=True,
        dag=dag,
)

```
