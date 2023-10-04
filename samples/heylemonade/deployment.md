
```plantuml
@startuml kubernetes

footer Kubernetes Plant-UML
scale max 1024 width

skinparam nodesep 10
skinparam ranksep 10

' Kubernetes
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

!define KubernetesPuml https://raw.githubusercontent.com/dcasati/kubernetes-PlantUML/master/dist
!define AWSPuml https://raw.githubusercontent.com/awslabs/aws-icons-for-plantuml/v14.0/dist

!includeurl KubernetesPuml/kubernetes_Common.puml
!includeurl KubernetesPuml/kubernetes_Context.puml
!includeurl KubernetesPuml/kubernetes_Simplified.puml

!includeurl KubernetesPuml/OSS/KubernetesSvc.puml
!includeurl KubernetesPuml/OSS/KubernetesIng.puml
!includeurl KubernetesPuml/OSS/KubernetesPod.puml
!includeurl KubernetesPuml/OSS/KubernetesDeploy.puml

!include AWSPuml/AWSCommon.puml
!include AWSPuml/Storage/SimpleStorageService.puml
!include AWSPuml/NetworkingContentDelivery/ElasticLoadBalancingApplicationLoadBalancer.puml

Person(user, "User")

left to right direction

' Kubernetes Components
Cluster_Boundary(cluster, "Kubernetes Cluster") {
    Namespace_Boundary(ns, "heylemonade") {
        KubernetesIng(ingress, "api", "")

        KubernetesSvc(svcstaging, "api-staging", "")
        KubernetesSvc(svcproduction, "api-production", "")

        KubernetesSvc(svcredis, "redis", "")
        KubernetesSvc(svcseq, "seq", "")

        KubernetesDeploy(deployproduction, "api-1.1.189", "")
        KubernetesDeploy(deploystaging, "api-1.1.204", "")

        Node_Boundary(node1, "Node 1") {
            KubernetesPod(pod1, "api1", "") #Technology
            KubernetesPod(pod3, "api3", "") #Application

            KubernetesPod(podredis, "redis", "") 
            KubernetesPod(podseq, "seq", "") 
        }

        Node_Boundary(node2, "Node 2") {
            KubernetesPod(pod2, "api2", "") #Technology
            KubernetesPod(pod4, "api4", "") #Application
        }
    }
}

ElasticLoadBalancingApplicationLoadBalancer(alb, "ALB", "api.domain.com, api.staging.domain.com")

Rel(user,alb, "HTTPS")
Rel(alb, ingress, "HTTPS")

Rel(ingress,svcstaging,"1.1.204")
Rel(ingress,svcproduction,"1.1.189")

Rel_U(deploystaging,pod1, "1.1.190")
Rel_U(deploystaging,pod2, "1.1.190")
Rel_U(deployproduction,pod3,"1.1.204")
Rel_U(deployproduction,pod4,"1.1.204")

Rel(svcstaging,pod1,"1.1.204")
Rel(svcstaging,pod2,"1.1.204")
Rel(svcproduction,pod3,"1.1.189")
Rel(svcproduction,pod4,"1.1.189")

Rel(svcredis,podredis,"")
Rel(svcseq,podseq,"")


@enduml
```