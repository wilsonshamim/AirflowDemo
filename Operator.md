1. import operators

from airflow.operators import PythonOperator, DummyOperator,BashOperator

python operator:

py_op = PythonOperator(
    task_id='two',
    python_callable=test,
    dag=dag,
    )
 ---------------------------------------------  
dummy operator:

dummy_op = DummyOperator(task_id='three', dag=dag)
-------------------------------------------------
Bash operator:
bash_op =  BashOperator(
    task_id='print_date',
    bash_command='date',
    dag=dag,
)

or call shell script
t2 = BashOperator(
    task_id='bash_example',
    bash_command="/home/batcher/test.sh ",
    dag=dag)
