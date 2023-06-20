:original_name: cce_productdesc_0010.html

.. _cce_productdesc_0010:

Mappings Between CCE and Kubernetes Terms
=========================================

Kubernetes (K8s) is an open-source system for automating deployment, scaling, and management of container clusters. It is a container orchestration tool and a leading solution based on the distributed architecture of the container technology. Kubernetes is built on the open-source Docker technology that automates deployment, resource scheduling, service discovery, and dynamic scaling of containerized applications.

This topic describes the mappings between CCE and Kubernetes terms.

.. table:: **Table 1** Mappings between CCE and Kubernetes terms

   ================================ =====================
   CCE                              Kubernetes
   ================================ =====================
   Cluster                          Cluster
   Node                             Node
   Node pool                        NodePool
   Container                        Container
   Image                            Image
   Namespace                        Namespace
   Deployment                       Deployment
   StatefulSet                      StatefulSet
   DaemonSet                        DaemonSet
   Job                              Job
   Cron job                         CronJob
   Pod                              Pod
   Service                          Service
   ClusterIP                        Cluster IP
   NodePort                         NodePort
   LoadBalancer                     LoadBalancer
   Layer-7 load balancing (ingress) Ingress
   Network policy                   NetworkPolicy
   Chart                            Template
   ConfigMap                        ConfigMap
   Secret                           Secret
   Label                            Label
   Label selector                   LabelSelector
   Annotation                       Annotation
   Volume                           PersistentVolume
   PersistentVolumeClaim            PersistentVolumeClaim
   Auto scaling                     HPA
   Node affinity                    NodeAffinity
   Node anti-affinity               NodeAntiAffinity
   Pod affinity                     PodAffinity
   Pod anti-affinity                PodAntiAffinity
   Webhook                          Webhook
   Endpoint                         Endpoint
   Quota                            Resource Quota
   Resource limit                   Limit Range
   ================================ =====================
