# Deploy Laravel with ECS

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

    For EC2 launch type:
    ```
    "requiresCompatibilities": [
        "EC2"
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

3. Create a IAM role with the permisssions needed for EC2 instance to access ECS. [Learn more](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/instance_IAM_role.html).

    > Policy can be found under AWS Managed type with the name `AmazonEC2ContainerServiceforEC2Role`

4. Create ECS cluste with `EC2 Linux + Networking` using AWS ECS console.

    > EC2 instances will not attach to the cluster if the instance IAM role selected doesn't have the right permission.

    You can attach EC2 instances to the cluster manually. [Learn more](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/launch_container_instance.html).

5. (Optional) Create a Application Load Balancer in Amazon EC2 console. [Learn more](https://docs.aws.amazon.com/AmazonECS/latest/userguide/create-application-load-balancer.html).

    > Security Groups **Target type** must be set as `ip` when service's task definition uses the `awsvpc` network mode.
    
    Learn more about ECS [Service Load Balancing](https://docs.aws.amazon.com/AmazonECS/latest/userguide/service-load-balancing.html).

6. Create a Service for the cluster.

    You can generate this service definition template using the following AWS CLI command:
    ```
    aws ecs create-service --generate-cli-skeleton > SERVICE_DEFINITION.json
    ```

     For EC2 launch type:
    ```
    "launchType": "EC2",
    ```

    Learn more about [Service Definition Parameters](https://docs.aws.amazon.com/AmazonECS/latest/userguide/service_definition_parameters.html).

    To use a JSON file for service definitions:
    ```
    aws ecs create-service --cli-input-json file://PATH/TO/SERVICE_DEFINITION.json
    ```
    > Tasks will start running in the cluster with `desiredCount`.
    > Service load balancing settings can only be set on service creation.


## Links