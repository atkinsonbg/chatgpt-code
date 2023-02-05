Assumes:
- Cluster is built
- ALB is built
- Route 53 is built

```
---
AWSTemplateFormatVersion: '2010-09-09'
Resources:
  FargateTaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      ContainerDefinitions:
        - Name: my-container
          Image: my-image-repository/my-image:latest
          PortMappings:
            - ContainerPort: 80

  FargateService:
    Type: AWS::ECS::Service
    Properties:
      TaskDefinition: !Ref FargateTaskDefinition
      Cluster: my-fargate-cluster
      DesiredCount: 1
      LaunchType: FARGATE
      NetworkConfiguration:
        AwsvpcConfiguration:
          AssignPublicIp: ENABLED
          SecurityGroups:
            - sg-0123456789abcdef0
            - sg-0123456789abcdef1
          Subnets:
            - subnet-0123456789abcdef0
            - subnet-0123456789abcdef1

  LoadBalancerListener:
    Type: AWS::ElasticLoadBalancingV2::Listener
    Properties:
      LoadBalancerArn: arn:aws:elasticloadbalancing:us-west-2:123456789012:loadbalancer/app/my-existing-load-balancer/abcdef0123456789
      Port: 80
      Protocol: HTTP
      DefaultActions:
        - Type: forward
          TargetGroupArn: !Ref LoadBalancerTargetGroup

  LoadBalancerTargetGroup:
    Type: AWS::ElasticLoadBalancingV2::TargetGroup
    Properties:
      Name: my-target-group
      Port: 80
      Protocol: HTTP
      TargetType: ip
      VpcId: vpc-0123456789abcdef0
      Targets:
        - Id: !Ref FargateTaskDefinition
          Port: 80

```
