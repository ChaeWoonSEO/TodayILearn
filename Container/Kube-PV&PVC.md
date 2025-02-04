# Persistant Volume, Persistant Volume Claim

k8s 에서 storage 를 관리하는데 2가지 옵션을 생각할 수 있다. 일반적인 Volume 을 통해 사용하는것과 Persistent Volume 을 통해 사용하는것이다. 

일반적으로 Volume 을 사용하는것은 파드 내부에 Volume을 부착하여 storage 를 사용하는것이다.

Volume 을 파드 내부에 부착하여 사용하게 된다면, 해당 파드 내 모든 컨테이너들이 같이 공유하여 사용 할 수 있다.

Volume 을 사용하는 예시는 로그 수집이 있을 수 있다. 하나의 파드에서 다수의 컨테이너를 사용 할 경우 로그 수집을 하려면 쿠버네티스에선 내부적으로 로그 수집을 하는 툴을 기본적으로 내장되어있지 않기 때문에 이를 추가적인 addon을 설치하여 사용해야한다. ex) fluentd

하지만 Volume 을 사용하는 것에 대한 단점은 Volume 을 통해 사용되는 스토리지는 임시 스토리지이기 때문에 파드가 삭제될 경우 볼륨도 함께 삭제된다.

반면에 Persistent Volume은 약간 다른 형태를 가진다. Persistent Volume 은 영구적인 상태 저장형 볼륨으로 PV 를 삭제하더라도 볼륨은 삭제되지 않는다. 

PV 를 클러스터 내 모든 리소스가 사용가능 하도록 만들어주는것이 PVC(Persistent Volume Claim) 이다.

PV와 PVC 는 1:1 로 서로 한개의 리소스만 할당하여 사용할 수 있다.PVC 생성시 storage class 를 공란으로 둔다면, 이는 수동적으로 PV 를 생성하는것이며, storage class 를 지정한다면, 해당 지정한 storage class 형태로 PV 를 생성해준다.

PersistentVolumeReclaimPolicy 라는 옵션이 있다. 이는 PVC 를 삭제할 시 남아있는 PV의 형태를 지정할 수 있는것인데, Recycle 일 경우 PVC를 삭제한 후 볼륨을 유지한다. 단, 유지한 볼륨의 내부 데이터는 모두 삭제되기 때문에 사용에 유의 해야한다.
Delete 일 경우 PVC를 삭제한 후 PV 볼륨또한 삭제한다. Retain 의 경우 PVC 를 삭제한 후 볼륨을 유지하며 볼륨내 내부 데이터 또한 유지된다.

만약 Retain 된 볼륨을 다시 사용하게 된다면, 해당 PV 의 UID 는 다르기 때문에 유지는 되지만 바로 PVC 에 할당하여 사용을 할 순 없다. 하지만 PVC 에 할당시 PV의 PVC resource UID 를 새로운 PVC 의 UID 로 변경한다면 새로운 PVC 에 사용이 가능하다. 이는 강제로 적용하는것이기 때문에 추천하는 방법은 아니다.

일반적으로 PV 및 Volume 은 해당 컨테이너에서 마운트가 되지 않는 환경이라면, PV 및 PVC 와 Volume 을 사용 할 수 없습니다.

<Limit Range 및 Resource Quota 내용>