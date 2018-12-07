# Deploy Laravel with ECS Fargate

## Setup

1. [Install](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) and [Configure](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) AWS CLI.
2. Authenticate Docker to an Amazon ECR registry.
    ```
    aws ecr get-login --no-include-email
    ```
3. Push Docker images to Amazon ECR repository. [Learn more](https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-push-ecr-image.html).
4. Create the task execution IAM role. 

    - Using AWS CLI. Follow Step 1 in this [tutorial](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cli-tutorial-fargate.html)
    
    - [Using IAM Console](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html)

    > IAM Role might have been created if you went through the Get Started wizard.

## Deploy Laravel with ECS

1. Register a Task Definition.

    For Fargate launch type:
    ```
    "requiresCompatibilities": [
        "FARGATE"
    ]
    ```

    You can generate this task definition template using the following AWS CLI command:
    ```
    aws ecs register-task-definition --generate-cli-skeleton > TASK_DEFINITION.json
    ```
    Learn more about [Task Definition Parameters](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html).

    To use a JSON file for task definitions:
    ```
    aws ecs register-task-definition --cli-input-json file://PATH/TO/TASK_DEFINITION.json
    ```
2. Create a CloudWatch Log group.  
    - Navigate to the CloudWatch console and choose Logs. 
    - On the Actions menu, choose Create log group. With the name defined under `logConfiguration` in task definition.  

3. Create ECS cluster.
    ```
    aws ecs create-cluster --cluster-name FARGATE_CLUSTER_NAME
    ```

4. (Optional) Create a Application Load Balancer in Amazon EC2 console. [Learn more](https://docs.aws.amazon.com/AmazonECS/latest/userguide/create-application-load-balancer.html).

    > Security Groups **Target type** must be set as `ip` when service's task definition uses the `awsvpc` network mode.
    
    Learn more about ECS [Service Load Balancing](https://docs.aws.amazon.com/AmazonECS/latest/userguide/service-load-balancing.html).

5. Create a Service for the cluster.

    You can generate this service definition template using the following AWS CLI command:
    ```
    aws ecs create-service --generate-cli-skeleton > SERVICE_DEFINITION.json
    ```

     For Fargate launch type:
    ```
    "launchType": "FARGATE",
    ```

    Learn more about [Service Definition Parameters](https://docs.aws.amazon.com/AmazonECS/latest/userguide/service_definition_parameters.html).

    To use a JSON file for service definitions:
    ```
    aws ecs create-service --cli-input-json file://PATH/TO/SERVICE_DEFINITION.json
    ```
    > Tasks will start running in the cluster with `desiredCount`.
    > Service load balancing settings can only be set on service creation.


## Links

* ECS Fargate is usually 2-3 times more expensive than an ECS in EC2 Launch Type Model. Check the [ECS pricing model](https://aws.amazon.com/ecs/pricing/).
    
