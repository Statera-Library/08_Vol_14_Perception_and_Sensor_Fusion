**Volume 14. Perception and Sensor Fusion**

# Chapter 02. Camera Perception

## 02.01. 2D Object Detection YOLO RT DETR Architecture [w/Code]

![](images/image1.png){width="7.268055555555556in" height="7.268055555555556in"}

현대적인 2차원 객체 검출(2D Object Detection)은 카메라 영상(Camera Image)을 이미지 평면(Image Plane)에 어떤 객체가 존재하며 어디에 위치하는지를 설명하는 구조화된 관측 정보(Structured Observation)로 변환한다. 로봇 인지(Robot Perception)에서 이러한 검출 결과는 원시 시각 센싱(Raw Visual Sensing)과 객체 추적(Object Tracking), 충돌 회피(Collision Avoidance), 의미 지도(Semantic Mapping), 조작(Manipulation), 내비게이션(Navigation) 같은 상위 기능을 연결하는 중요한 역할을 한다.

일반적인 2차원 객체 검출기(2D Object Detector)는 RGB 영상을 텐서(Tensor) 형태로 입력받아 객체 클래스(Class), 신뢰도 점수(Confidence Score), 경계 상자(Bounding Box)를 포함하는 객체 후보(Object Hypothesis) 집합을 출력한다. 경계 상자는 중심 좌표와 너비·높이 또는 서로 마주 보는 두 모서리 좌표로 표현된다. 학습 과정에서는 예측 객체와 정답 데이터(Ground Truth)를 위치 및 분류 목적함수(Objective)를 통해 비교하고, 추론(Inference) 단계에서는 이를 로봇 소프트웨어가 사용할 수 있는 간결한 검출 정보로 변환한다.

YOLO는 객체 위치 추정(Object Localization)과 분류(Classification)를 하나의 통합 신경망 파이프라인(Neural Network Pipeline)에서 수행하는 단일 단계 검출기(Single-Stage Detector)의 대표적인 철학을 따른다. 먼저 후보 영역(Candidate Region)을 생성한 뒤 각각을 별도로 처리하는 대신, 전체 영상에서 시각 특징(Visual Feature)을 추출하고 이 특징으로부터 객체를 직접 예측한다. 이러한 구조는 카메라 프레임(Camera Frame)을 지속적으로 처리하면서 제어와 내비게이션에 필요한 낮은 지연시간(Latency)을 유지해야 하는 로봇 시스템에 특히 적합하다.

실용적인 YOLO 구조는 백본(Backbone), 넥(Neck), 검출 헤드(Detection Head)의 연속적인 기능으로 이해할 수 있다. 백본은 픽셀(Pixel)을 계층적 특징 표현(Hierarchical Feature Representation)으로 변환하며, 초기 계층은 국부적인 에지(Edge)와 질감(Texture)을 보존하고 깊은 계층으로 갈수록 의미적 정보(Semantic Information)를 표현한다. 넥은 서로 다른 공간 해상도(Spatial Resolution)의 특징을 결합하고, 검출 헤드는 이를 경계 상자 좌표, 객체 클래스, 신뢰도 값으로 변환한다.

다중 스케일 처리(Multi-Scale Processing)는 이동 로봇(Mobile Robot)에서 특히 중요하다. 객체의 겉보기 크기(Apparent Size)는 로봇과의 거리에 따라 빠르게 변하기 때문이다. 가까운 보행자(Pedestrian)는 카메라 영상의 상당 부분을 차지하지만 멀리 있는 보행자는 불과 몇 개의 픽셀로 표현될 수 있다. 특징 피라미드(Feature Pyramid) 구조는 높은 해상도의 공간 정보와 깊은 의미 특징을 결합하여 서로 다른 크기의 객체를 별도의 네트워크 없이 검출할 수 있도록 한다.

YOLO 구현은 앵커 기반 예측(Anchor-Based Prediction)에서 점차 앵커 프리(Anchor-Free) 설계로 발전해 왔다. 앵커 기반 검출기는 미리 정의된 경계 상자 형태와 예측 결과를 연결하므로 예상 객체 분포에 적합한 앵커 설정이 필요하다. 반면 앵커 프리 방식은 특징 맵(Feature Map)의 위치에서 객체 위치와 상자 형상을 보다 직접적으로 예측한다. 카메라, 환경, 객체 크기가 달라지는 로봇 응용에서는 수동 앵커 설계 의존성을 줄임으로써 인지 파이프라인의 적응성과 이식성을 높일 수 있다.

전통적인 밀집 검출기(Dense Detector)는 하나의 물리적 객체에 대해 서로 겹치는 여러 예측 결과를 생성할 수 있다. 비최대 억제(Non-Maximum Suppression, NMS)는 신뢰도를 기준으로 검출 결과를 정렬하고, 일반적으로 교집합 대비 합집합(Intersection over Union, IoU)을 기준으로 높은 신뢰도의 상자와 크게 겹치는 다른 상자를 제거한다. NMS의 계산량은 신경망에 비해 작지만 별도의 후처리(Post-Processing) 단계를 추가하며, 임계값(Threshold)은 객체가 겹치거나 밀집된 환경에서 검출 동작에 영향을 미친다.

RT-DETR은 기존의 밀집 YOLO 구조보다는 트랜스포머(Transformer) 기반 DETR 계열에서 실시간 객체 검출(Real-Time Object Detection)에 접근한다. DETR은 객체 검출을 집합 예측 문제(Set Prediction Problem)로 재구성한다. 고정된 객체 쿼리(Object Query) 집합이 인코딩된 영상 특징과 상호작용하여 객체 집합을 예측하며, 학습 과정에서는 이분 매칭(Bipartite Matching)을 통해 예측 객체와 정답 객체 사이의 일대일 관계를 구성한다. 이를 통해 앵커와 중복 제거 절차에 대한 구조적 의존성을 줄인다.

트랜스포머 구조(Transformer Architecture)는 중요한 구조적 차이를 제공한다. 합성곱 검출기(Convolutional Detector)는 주로 공간적으로 국부적인 연산을 여러 계층에 걸쳐 결합하여 특징을 생성하지만, 어텐션 메커니즘(Attention Mechanism)은 공간 특징 사이의 관계를 보다 명시적으로 모델링할 수 있다. 이를 통해 객체 쿼리는 영상 문맥(Image Context)을 활용하여 예측할 수 있다. 그러나 초기 DETR은 학습 수렴(Convergence)과 계산 비용 측면의 문제가 있었으며, RT-DETR은 실시간 검출에 적합하도록 특징 처리와 쿼리 선택을 재구성한다.

RT-DETR은 다중 스케일 합성곱 특징(Multi-Scale Convolutional Feature)과 효율적인 하이브리드 인코더(Hybrid Encoder)를 결합하여 고해상도 특징 맵 처리 비용을 제한한다. 모든 특징 계층에 고비용 트랜스포머 연산을 동일하게 적용하는 대신, 동일 스케일 내부 특징 상호작용(Intra-Scale Feature Interaction)과 스케일 간 특징 융합(Cross-Scale Feature Fusion)을 구분한다. 이를 통해 유용한 다중 해상도 정보를 유지하면서 불필요한 계산을 줄여 엣지 컴퓨터(Edge Computer)에서 지속적인 검출을 수행하기에 적합하도록 한다.

또 다른 중요한 요소는 쿼리 선택(Query Selection)이다. 트랜스포머 검출기는 제한된 수의 디코더 쿼리(Decoder Query)를 이용해 객체 후보를 표현하므로 초기 쿼리의 품질은 학습 수렴과 추론 효율에 영향을 준다. RT-DETR은 정보량이 높은 인코더 특징(Encoder Feature)을 객체 쿼리로 선택한 뒤 트랜스포머 디코더(Transformer Decoder)를 통해 클래스와 경계 상자 예측을 점진적으로 정제한다. 이를 통해 종단간 집합 예측(End-to-End Set Prediction)의 원칙을 유지하면서 실시간 비전(Real-Time Vision)에 필요한 지연시간을 목표로 한다.

따라서 YOLO와 RT-DETR은 동일한 로봇 인지 요구사항을 해결하는 서로 연관되면서도 구조적으로 다른 접근법이다. YOLO는 합성곱 특징 추출(Convolutional Feature Extraction), 다중 스케일 융합(Multi-Scale Fusion), 효율적인 예측 헤드(Prediction Head)를 중심으로 최적화된 밀집 검출 파이프라인을 강조한다. RT-DETR은 합성곱 특징 추출과 트랜스포머 기반 집합 예측 및 객체 쿼리를 결합한다. 따라서 적합한 구조의 선택은 정확도뿐 아니라 지연시간, 메모리 사용량, 영상 해상도, 가속기 지원, 학습 데이터와 시스템 요구사항을 함께 고려해야 한다.

로봇 시스템에서 검출기 성능은 독립적인 벤치마크(Benchmark)가 아니라 전체 인지-제어 파이프라인(Perception-Control Pipeline)의 일부로 평가해야 한다. 평균 정밀도(Mean Average Precision, mAP)는 검출 품질을 평가하는 중요한 지표이며 교집합 대비 합집합(IoU)은 위치 중첩 정도를 나타내지만, 이러한 지표만으로 로봇의 안전한 운용 여부를 결정할 수 없다. 프레임 지연시간(Frame Latency), 처리량(Throughput), 시간적 안정성(Temporal Stability), 최소 검출 객체 크기, 미검출(False Negative), 신뢰도 보정(Confidence Calibration), 모션 블러(Motion Blur)와 조명 변화에 대한 성능도 중요하다.

입력 해상도(Input Resolution)는 기본적인 공학적 절충 관계(Engineering Trade-Off)를 형성한다. 영상 크기를 증가시키면 멀리 있거나 물리적으로 작은 객체에 대한 정보를 더 많이 보존할 수 있지만 특징 맵 크기, 메모리 트래픽(Memory Traffic), 추론 연산량도 증가한다. 반대로 해상도를 낮추면 처리량은 향상되지만 조기 장애물 검출에 필요한 시각 정보가 손실될 수 있다. 따라서 카메라 해상도와 검출기 입력 크기는 검출 거리, 시야각(Field of View), 대상 크기, 로봇 속도와 요구 반응시간을 기준으로 결정해야 한다.

지연시간 역시 종단간(End-to-End) 관점에서 고려해야 한다. 카메라 노출(Camera Exposure)과 판독(Readout), 영상 전송, 전처리(Preprocessing), 신경망 추론, 후처리, 메시지 전송, 추적, 경로 계획(Planning), 제어가 모두 로봇의 반응시간 예산(Reaction-Time Budget)을 사용한다. 높은 초당 프레임 수(Frames Per Second, FPS)를 달성하는 검출기라도 버퍼링(Buffering)이나 비동기 파이프라인(Asynchronous Pipeline)으로 오래된 관측 정보를 전달한다면 부적합할 수 있다. 따라서 영상 획득 시점에 타임스탬프(Timestamp)를 부여하고 이를 전체 인지 스택에서 유지해야 한다.

시간적 동작(Temporal Behavior)은 벤치마크 검출과 실제 로봇 인지를 구분하는 또 다른 요소다. YOLO와 RT-DETR은 일반적으로 개별 프레임을 처리하지만 이동 로봇은 연속적인 장면을 경험한다. 조명, 관측 시점(Viewpoint), 부분 가림(Partial Occlusion), 영상 잡음의 작은 변화만으로도 인접 프레임 사이에서 경계 상자와 신뢰도가 변동할 수 있다. 객체 추적, 시간 필터링(Temporal Filtering), 자기 운동 보상(Ego-Motion Compensation), 시간 융합(Temporal Fusion)을 이용하면 프레임 단위 검출을 계획과 제어에 적합한 안정적인 객체 상태로 변환할 수 있다.

학습 데이터(Training Data)는 로봇의 실제 운용 영역(Operational Domain)을 반영해야 한다. 범용 데이터셋(Generic Dataset)은 유용한 사전학습 시각 표현(Pretrained Visual Representation)을 제공하지만 실제 카메라는 설치 위치에 따른 시점, 렌즈 왜곡(Lens Distortion), 진동, 날씨, 반사면, 작업복, 팔레트, 산업 장비 등 일반 데이터셋에서 드문 조건을 경험한다. 따라서 실제 운용 영상을 포함한 미세조정(Fine-Tuning)이 필요하며, 카메라 장착 높이와 관측 각도는 객체 외형과 가림 특성에 큰 영향을 주므로 실제 설치 위치에서 수집한 데이터가 특히 중요하다.

배포(Deployment) 과정에서는 신뢰도 임계값과 알려지지 않은 조건(Unknown Condition)을 명시적으로 관리해야 한다. 높은 임계값은 오검출(False Positive)을 줄일 수 있지만 멀리 있거나 일부 가려진 위험 객체를 제거할 수 있으며, 낮은 임계값은 재현율(Recall)을 높이는 대신 불확실한 검출 결과를 증가시킨다. 이러한 결정은 하위 위험 처리 로직과 연결되어야 한다. 검출 신뢰도를 물리적 안전 확률로 직접 해석해서는 안 되며, 안전 중요 동작(Safety-Critical Behavior)은 적절한 중복성(Redundancy), 모니터링, 보수적 폴백(Fallback) 없이 하나의 신경망 예측에만 의존해서는 안 된다.

엣지 로봇(Edge Robot)에서 모델 선택은 단순히 검출기 계열을 결정하는 것 이상을 의미한다. 전체 구현에서는 GPU 메모리, 텐서 정밀도(Tensor Precision), 지원 연산자(Operator), 전처리 오버헤드, 배치 크기(Batch Size), 열적 한계(Thermal Limit), 추론 런타임 최적화(Inference Runtime Optimization)를 함께 고려해야 한다. 목표 가속기에서 지원된다면 FP16 또는 INT8 실행을 통해 계산량과 메모리 요구량을 줄일 수 있지만, 양자화(Quantization)의 영향은 안전 관련 객체 클래스와 어려운 운용 조건을 대상으로 반드시 검증해야 한다.

로봇 소프트웨어 아키텍처(Robot Software Architecture)에 통합할 때 검출 결과는 일반적으로 영상 참조 정보, 경계 상자, 클래스, 신뢰도, 프레임 식별자(Frame Identifier)를 포함하는 타임스탬프 기반 메시지로 제공된다. 보정 정보(Calibration Information)는 영상 검출 결과를 카메라 기하(Camera Geometry)와 연결하며, 시간 동기화(Synchronization)는 이후 LiDAR, 레이더(Radar), 깊이(Depth), 관성 센서(Inertial Sensor)와의 융합을 가능하게 한다. 따라서 검출기는 단순한 AI 모델이 아니라 정의된 입출력, 시간 요구사항, 진단 기능, 장애 상태를 갖는 실시간 소프트웨어 구성요소로 다루어야 한다.

견고한 구현에서는 동일한 인지 인터페이스(Perception Interface) 뒤에서 여러 검출기 백엔드(Detector Backend)를 지원할 수 있다. 최소 지연시간과 성숙한 배포 도구가 중요할 경우 YOLO를 기본 구성으로 사용할 수 있으며, 트랜스포머 기반 집합 예측과 문맥적 특징 상호작용(Contextual Feature Interaction)이 유리한 경우 RT-DETR을 대안으로 사용할 수 있다. 전처리, 추론, 출력 정규화(Output Normalization), 시각화, 추적, ROS2 인터페이스를 모듈화하면 특정 검출기에 대한 의존성이 전체 로봇 소프트웨어로 확산되는 것을 방지할 수 있다.

궁극적으로 YOLO와 RT-DETR은 2차원 객체 검출이 독립적인 컴퓨터 비전(Computer Vision) 작업에서 자율 기계(Autonomous Machine)를 위한 재사용 가능한 인지 서비스(Perception Service)로 발전하는 과정을 보여준다. 검출기는 카메라 픽셀을 의미적 객체 후보로 변환하지만, 실제 로봇 지능은 이러한 결과가 동기화되고 검증되며 추적되고 기하 정보와 융합된 뒤 요구되는 지연시간 내에 전달될 때 형성된다. 이러한 역할은 카메라 인지에서 LiDAR, 레이더, 관성 인지, 다중 센서 융합(Multi-Sensor Fusion), 3차원 장면 이해(3D Scene Understanding), 피지컬 AI 인지(Physical AI Perception)로 확장되는 전체 인지 구조의 기반이 된다.

## 02.02. Instance and Panoptic Segmentation for Robots [w/Code]

![](images/image2.png){width="7.268055555555556in" height="7.268055555555556in"}

인스턴스 분할(Instance Segmentation)은 객체 검출(Object Detection)을 확장하여 어떤 객체가 존재하는지를 파악하는 것뿐만 아니라 각각의 개별 객체 인스턴스(Object Instance)에 속하는 정확한 픽셀(Pixel)을 결정한다. 로봇에서는 동일한 클래스(Class)에 속하는 객체들을 공간적으로 서로 구분할 수 있기 때문에 경계 상자(Bounding Box)보다 풍부한 표현을 제공한다. 이를 통해 개별 보행자, 팔레트, 차량, 컨테이너, 도구 등을 구별하고 내비게이션(Navigation), 조작(Manipulation), 장애물 회피(Obstacle Avoidance), 장면 이해(Scene Understanding)에 필요한 상세한 기하 정보를 제공할 수 있다.

의미 분할(Semantic Segmentation)은 관련된 각 픽셀에 클래스 레이블(Class Label)을 할당하지만 동일한 클래스에 속하는 개별 객체들을 서로 구분하지 않는다. 인스턴스 분할은 픽셀 수준 표현(Pixel-Level Representation)에 객체 식별 정보(Object Identity)를 추가한다. 예를 들어 하나의 카메라 영상에 세 명의 사람이 나타나면 의미 분할은 해당 픽셀을 모두 사람(Person)으로 분류하지만, 인스턴스 분할은 세 개의 서로 다른 마스크(Mask)를 생성한다. 이러한 차이는 로봇이 환경의 의미적 구성뿐만 아니라 개별 물리적 객체를 추론해야 할 때 필수적이다.

파놉틱 분할(Panoptic Segmentation)은 의미 분할과 인스턴스 분할을 결합하여 전체 영상을 하나의 통합된 표현으로 구성한다. 픽셀에 의미적 카테고리(Semantic Category)를 할당하면서 셀 수 있는 객체에는 개별 식별 정보(Instance Identity)를 부여한다. 사람, 차량, 상자, 로봇과 같은 사물(Things)은 개별 인스턴스 식별자를 가지며, 도로, 바닥, 벽, 하늘, 잔디, 식생과 같은 배경 영역(Stuff)은 연속적인 의미 영역으로 표현된다. 결과적으로 카메라에 보이는 의미 있는 모든 영역을 설명하는 구조화된 장면 표현이 생성된다.

세 가지 분할 방식의 차이는 각각 어떤 질문에 답하는가를 통해 이해할 수 있다. 의미 분할은 각 픽셀이 어떤 카테고리에 속하는지를 결정하고, 인스턴스 분할은 해당 픽셀이 어떤 개별 객체에 속하는지를 결정하며, 파놉틱 분할은 카테고리와 인스턴스 정보를 하나의 일관된 장면 표현(Scene Representation)으로 결합한다. 내비게이션은 주행 가능 영역과 장애물 영역을 주로 필요로 하고, 조작은 개별 대상의 정확한 마스크를 요구하며, 일반적인 장면 이해는 객체와 환경 표면을 모두 포함하는 완전한 표현으로부터 이점을 얻는다.

인스턴스 분할 아키텍처(Instance Segmentation Architecture)는 일반적으로 시각 특징 추출(Visual Feature Extraction)과 객체 인식(Object Recognition), 위치 추정(Localization), 마스크 예측(Mask Prediction)을 위한 메커니즘을 결합한다. 백본(Backbone)은 영상을 계층적 특징 맵(Hierarchical Feature Map)으로 변환하고, 다중 스케일 처리(Multi-Scale Processing)는 서로 다른 크기의 객체 정보를 보존한다. 검출 기반 구조는 먼저 객체 후보를 찾은 뒤 각 인스턴스의 마스크를 생성할 수 있으며, 새로운 쿼리 기반 구조(Query-Based Architecture)는 학습된 객체 쿼리(Object Query)로부터 클래스와 마스크를 직접 예측할 수 있다.

마스크 예측은 기존의 경계 상자 검출보다 더 세밀한 공간 정보(Spatial Information)를 요구한다. 경계 상자는 객체 주변의 배경 픽셀을 포함하지만, 분할 마스크는 객체의 실제 가시 윤곽(Visible Contour)을 따라가도록 한다. 정확한 마스크는 객체가 서로 겹치거나 로봇 매니퓰레이터(Manipulator)가 파지 가능한 영역을 선택해야 할 때, 또는 자율 로봇이 장애물의 경계를 추정해야 할 때 특히 유용하다. 그러나 영상 마스크는 가시 영역의 2차원 투영(2D Projection)을 표현할 뿐이며, 그 자체로 완전한 3차원 기하 정보를 제공하지는 않는다.

다중 스케일 표현(Multi-Scale Representation)은 로봇 카메라가 매우 다양한 거리의 객체를 관찰하기 때문에 중요하다. 가까운 팔레트는 수천 개의 픽셀을 차지할 수 있지만 멀리 있는 보행자는 작은 영상 영역으로만 나타날 수 있다. 특징 피라미드(Feature Pyramid)와 관련된 다중 해상도 메커니즘(Multi-Resolution Mechanism)은 높은 해상도의 공간 정보와 깊은 의미 특징을 결합한다. 충분한 고해상도 표현이 없으면 작은 객체가 특징 다운샘플링(Feature Downsampling) 과정에서 사라질 수 있으며, 의미적 문맥이 부족하면 시각적으로 유사한 객체를 구분하기 어려워질 수 있다.

가림(Occlusion)은 로봇 인스턴스 분할의 주요 과제 중 하나다. 보행자가 차량 뒤에 부분적으로 가려지거나, 팔레트가 장비에 의해 가려지거나, 객체 일부가 카메라 시야각(Field of View) 밖으로 벗어날 수 있다. 네트워크는 가시 픽셀만 관찰하므로 실제 객체 경계와 가림에 의해 형성된 경계를 구분해야 한다. 시간적 추적(Temporal Tracking), 깊이 추정(Depth Estimation), 다중 시점 관측(Multi-View Observation), 보완 센서(Complementary Sensor)를 활용하면 객체 식별 정보를 유지하고 가시 영역이 변화하는 상황에서도 보다 안정적인 해석이 가능하다.

파놉틱 분할은 전체 영상에 의미 및 인스턴스 정보를 할당해야 하므로 추가적인 일관성 요구사항(Consistency Requirement)을 가진다. 객체 영역은 배경 영역과 충돌하지 않아야 하고, 서로 다른 인스턴스는 구별되어야 하며, 의미적으로 연속된 표면은 일관되게 유지되어야 한다. 잘못된 분류, 객체 누락, 인스턴스 병합(Merged Instance), 마스크 분절(Fragmented Mask), 사물과 배경 영역 사이의 잘못된 할당 등 다양한 오류가 발생할 수 있다. 따라서 파놉틱 인지는 독립적인 객체 검출이나 분할보다 포괄적이지만 더욱 복잡한 표현이다.

평가(Evaluation)는 인식 품질과 분할 품질을 모두 고려해야 한다. 교집합 대비 합집합(Intersection over Union, IoU)은 예측 영역과 기준 영역의 중첩 정도를 측정하고, 클래스별 분할 지표(Class-Specific Segmentation Metric)는 의미적 정확도를 평가한다. 인스턴스 중심 평가는 개별 객체가 올바르게 검출되고 분할되었는지를 고려한다. 파놉틱 품질(Panoptic Quality, PQ)은 인식과 분할 요소를 결합하여 전체 결과를 평가한다. 로봇에서는 작은 경계 오차도 내비게이션과 조작에서 서로 다른 영향을 줄 수 있으므로 작업별 평가(Task-Specific Evaluation)가 추가되어야 한다.

시간적 안정성(Temporal Stability)은 이동 로봇에서 특히 중요하다. 개별 프레임의 분할 결과가 정확하더라도 카메라 움직임, 조명 변화, 부분 가림, 네트워크 신뢰도의 작은 변화로 인해 예측 마스크가 프레임 사이에서 흔들릴 수 있다. 이러한 불안정성은 추적, 지도 작성(Mapping), 경로 계획(Planning)으로 전파될 수 있다. 프레임 사이에서 마스크를 연결하고 객체 추적(Object Tracking), 광학 흐름(Optical Flow), 시간 특징 집계(Temporal Feature Aggregation)를 적용하면 프레임 단위 분할 결과를 물리적 객체와 환경 영역에 대한 지속적인 표현으로 변환할 수 있다.

분할은 깊이 정보(Depth Information)와 결합될 때 활용 가치가 크게 증가한다. 2차원 마스크는 어떤 영상 픽셀이 객체에 속하는지를 식별하며, 단안 깊이(Monocular Depth), 스테레오 비전(Stereo Vision), RGB-D 센싱(RGB-D Sensing), LiDAR를 이용하면 해당 픽셀에 거리와 3차원 기하 정보를 연결할 수 있다. 이렇게 생성된 객체 표현은 장애물 위치 추정, 파지 계획(Grasp Planning), 공간 측정, 의미 지도 작성(Semantic Mapping), 충돌 검사(Collision Checking)를 지원한다. 이는 카메라 분할이 보다 광범위한 기하학적 인지 아키텍처(Geometric Perception Architecture)의 의미 계층으로 기능한다는 것을 보여준다.

내비게이션에서 분할은 경계 상자 검출만 사용하는 것보다 주행 가능 영역(Traversable Region)과 주행 불가능 영역(Non-Traversable Region)을 더 정밀하게 분류할 수 있다. 바닥, 도로, 보도, 식생, 벽, 차량, 보행자, 미확인 영역을 내비게이션 시스템에서 서로 다르게 해석할 수 있다. 의미 또는 파놉틱 마스크는 이후 점유 지도(Occupancy Map)와 의미 비용 지도(Semantic Costmap)에 활용될 수 있다. 그러나 익숙하지 않은 객체나 불리한 조명, 도메인 변화(Domain Shift)에서 분할 네트워크가 실패할 수 있으므로 분할되지 않은 영역을 자유 공간(Free Space)으로 간주해서는 안 된다.

조작에서는 인스턴스 분할이 개별 목표 객체(Target Object)에 대한 명시적인 표현을 제공한다. 로봇 팔(Robot Arm)은 객체 마스크를 사용하여 깊이 측정값을 분리하고, 가시 기하 구조를 추정하며, 관심 영역(Region of Interest)을 계산하거나 6자유도 자세 추정(6DoF Pose Estimation)의 입력을 생성할 수 있다. 여러 객체가 빈(Bin) 내부에서 접촉하거나 겹치는 경우에는 개별 인스턴스를 분리하는 것이 특히 중요하다. 따라서 분할 결과는 단순한 시각화 결과가 아니라 자세 추정, 파지 계획, 조작 제어(Manipulation Control)와 연결된다.

카메라 조건(Camera Condition)은 분할 품질에 큰 영향을 미친다. 렌즈 왜곡(Lens Distortion), 모션 블러(Motion Blur), 그림자, 반사, 노출 변화(Exposure Change), 비, 안개, 오염, 진동, 조명 변화는 객체 외형과 경계를 변화시킬 수 있다. 따라서 학습 및 검증 데이터는 실제 운용 영역(Operational Domain)을 가능한 한 충실하게 반영해야 한다. 산업용 로봇과 실외 자율이동로봇(Outdoor AMR)은 일반적인 영상 데이터셋과 다른 카메라 높이와 관측 각도를 사용하기 때문에 실제 배포 환경에 특화된 데이터 수집과 미세조정(Fine-Tuning)이 중요하다.

입력 해상도(Input Resolution)는 경계 품질과 계산 비용 사이에 직접적인 절충 관계(Trade-Off)를 형성한다. 높은 해상도는 얇은 구조, 작은 객체, 세밀한 윤곽을 보존하지만 메모리 트래픽(Memory Traffic)과 추론 지연시간(Inference Latency)을 증가시킨다. 낮은 해상도는 처리량을 향상시키지만 정확한 마스크 생성에 필요한 정보를 제거할 수 있다. 마스크 자체도 원본 영상보다 낮은 해상도에서 생성한 뒤 업샘플링(Upsampling)할 수 있으므로 요구 검출 거리, 객체 크기, 플랫폼 속도, 사용 가능한 연산 자원을 기준으로 영상과 마스크 해상도를 결정해야 한다.

엣지 배포(Edge Deployment)에서는 신경망뿐만 아니라 전체 분할 파이프라인(Segmentation Pipeline)을 최적화해야 한다. 영상 획득, 크기 조정, 정규화(Normalization), 추론, 마스크 생성, 원본 영상 크기로의 복원, 후처리(Post-Processing), 메시지 직렬화(Message Serialization), 하위 모듈로의 전송이 모두 지연시간에 영향을 준다. FP16 또는 INT8 추론은 계산량과 메모리 사용량을 줄일 수 있지만 양자화(Quantization)가 작은 객체, 얇은 구조, 신뢰도, 세밀한 마스크 경계에 영향을 줄 수 있으므로 충분한 검증이 필요하다.

ROS2 기반 로봇 아키텍처에서는 분할 결과를 카메라 보정(Camera Calibration) 및 좌표 프레임(Coordinate Frame) 정보와 연결된 타임스탬프 기반 출력으로 발행할 수 있다. 메시지에는 의미 레이블, 인스턴스 식별자, 신뢰도 점수, 경계 상자, 이진 마스크(Binary Mask), 압축 마스크(Compressed Mask), 인덱스 분할 영상(Indexed Segmentation Image) 등이 포함될 수 있다. 원래의 영상 획득 타임스탬프를 유지하는 것은 깊이 카메라, LiDAR, 레이더(Radar), 관성측정장치(IMU) 데이터와 동기화하기 위해 중요하다.

모듈형 인지 아키텍처(Modular Perception Architecture)는 영상 전처리, 신경망 추론, 마스크 해석, 시간적 추적, 센서 융합(Sensor Fusion), 응용 인터페이스(Application Interface)를 분리할 수 있다. 이를 통해 전체 로봇 소프트웨어 스택을 다시 설계하지 않고도 서로 다른 분할 모델을 평가할 수 있다. 동일하게 정규화된 표현을 내비게이션, 조작, 의미 지도 작성, 장면 이해, 데이터 기록에 제공할 수 있으며, 배포 과정에서 GPU 사용률, 지연시간, 메모리 소비량, 실패율, 모델 정확도를 독립적으로 분석할 수 있다.

인스턴스 분할과 파놉틱 분할은 궁극적으로 카메라 영상을 단순한 픽셀 집합에서 객체와 환경 영역에 대한 구조화된 표현으로 변환한다. 인스턴스 분할은 객체별 마스크를 제공하고, 파놉틱 분할은 개별 객체와 연속적인 의미 표면을 결합하여 전체 장면을 표현한다. 카메라 인지(Camera Perception) 구조에서 이러한 기능은 2차원 객체 검출과 깊이 추정, 조감도 인지(BEV Perception), 3차원 객체 검출(3D Object Detection), 광학 흐름, 자세 추정, ROS2 통합, 엣지 배포를 자연스럽게 연결하는 기반을 형성한다.

## 02.03. Monocular Depth Estimation DPT DepthAnything [w/Code]

![](images/image3.png){width="7.268055555555556in" height="7.268055555555556in"}

단안 깊이 추정(Monocular Depth Estimation)은 하나의 RGB 영상으로부터 장면의 깊이(Scene Depth)를 예측하여 스테레오 대응(Stereo Correspondence)이나 능동형 깊이 센서(Active Depth Sensor) 없이도 로봇이 대략적인 기하 구조를 추론할 수 있도록 한다. 객체 검출(Object Detection)이나 분할(Segmentation)이 주로 영상 평면(Image Plane)의 의미 정보를 설명하는 것과 달리, 깊이 추정은 각 픽셀(Pixel)에 깊이와 관련된 값을 할당한다. 이렇게 생성된 밀집 깊이 표현(Dense Depth Representation)은 장애물 판단, 내비게이션(Navigation), 조작(Manipulation), 3차원 재구성(3D Reconstruction), 센서 융합(Sensor Fusion)에 활용될 수 있다.

근본적인 어려움은 하나의 영상만으로 절대적인 3차원 기하 구조(Absolute 3D Geometry)를 일반적으로 유일하게 결정할 수 없다는 점이다. 서로 다른 물리적 장면이 유사한 2차원 투영(2D Projection)을 생성할 수 있기 때문에 본질적으로 비정형 역문제(Ill-Posed Inverse Problem)가 된다. 사람은 원근법(Perspective), 상대적 객체 크기, 질감 기울기(Texture Gradient), 가림(Occlusion), 음영(Shading), 의미적 지식(Semantic Knowledge)과 같은 학습된 시각 단서를 이용해 이러한 모호성을 해결한다. 현대의 단안 깊이 네트워크도 대규모 학습 데이터에서 영상 외형과 장면 기하 사이의 통계적 관계를 학습한다.

단안 깊이 네트워크(Monocular Depth Network)는 RGB 영상을 입력받아 입력 영상과 공간적으로 대응되는 밀집 깊이 지도(Dense Depth Map) 또는 역깊이 지도(Inverse-Depth Map)를 생성한다. 각각의 출력 값은 해당 시선 방향(Viewing Direction)을 따라 장면의 거리 또는 상대 깊이를 추정한다. 학습 방식에 따라 예측 결과는 물리적 단위의 미터 깊이(Metric Depth), 스케일 불변 깊이(Scale-Invariant Depth), 상대 깊이 순서(Relative Depth Ordering), 시차 형태의 값(Disparity-Like Quantity), 또는 추가 정보가 있어야 미터 단위로 해석할 수 있는 아핀 불변 깊이(Affine-Invariant Depth)를 나타낼 수 있다.

따라서 로봇 응용에서는 상대 깊이(Relative Depth)와 미터 깊이(Metric Depth)를 명확히 구분해야 한다. 상대 깊이는 한 표면이 다른 표면보다 가까운지를 올바르게 판단할 수 있지만 절대 거리는 불확실할 수 있다. 미터 깊이는 일반적으로 미터 단위의 물리적 거리를 추정하며 충돌 검사(Collision Checking)나 기하학적 측정에 보다 직접적으로 사용할 수 있다. 깊이 지도가 시각적으로 그럴듯하더라도 모델, 보정(Calibration), 운용 영역(Operational Domain), 출력 정의가 이를 명시적으로 지원하지 않는다면 미터 단위로 정확하다고 해석해서는 안 된다.

밀집 예측 트랜스포머(Dense Prediction Transformer, DPT)는 단안 깊이 추정과 같은 밀집 예측 작업(Dense Prediction Task)을 위한 트랜스포머 중심 아키텍처(Transformer-Oriented Architecture)를 제시한다. 기존의 합성곱 특징 계층(Convolutional Feature Hierarchy)에만 의존하지 않고 비전 트랜스포머(Vision Transformer)가 생성한 표현을 사용하며, 여러 트랜스포머 단계의 특징을 영상 형태의 특징 맵(Feature Map)으로 다시 구성한다. 이후 이러한 표현을 점진적으로 융합하고 정제하여 트랜스포머 인코더(Transformer Encoder)가 학습한 전역 문맥 정보를 유지하면서 공간적 세부 정보를 복원한다.

트랜스포머 인코더는 영상을 패치(Patch)로 나누고 이를 토큰 표현(Token Representation)으로 변환한 뒤 자기 어텐션(Self-Attention)을 통해 토큰 사이의 관계를 처리한다. 이를 통해 비교적 넓은 전역 수용 영역(Global Receptive Field)을 확보하고 영상에서 멀리 떨어진 영역들이 서로의 표현에 영향을 줄 수 있다. 깊이 추정에서는 바닥, 벽, 객체, 수평선, 원근 구조처럼 영상 전체에 넓게 분포된 요소들 사이의 관계가 기하학적 해석에 중요하기 때문에 이러한 전역 문맥(Global Context)이 유용할 수 있다.

DPT는 토큰 중심의 트랜스포머 표현을 다시 밀집 공간 예측(Dense Spatial Prediction)으로 변환해야 한다. 따라서 중간 특징(Intermediate Feature)을 적절한 공간 해상도로 재구성하고 융합 단계(Fusion Stage)를 통해 의미적 문맥과 공간 구조를 점진적으로 결합한다. 디코더(Decoder)는 최종적으로 영상과 정렬된 밀집 깊이 예측을 생성한다. 이러한 인코더-재구성-융합(Encoder-Reassembly-Fusion) 구조는 고수준의 전역적 이해가 결국 세밀한 픽셀 단위 기하 구조로 다시 변환되어야 한다는 밀집 비전(Dense Vision)의 일반적인 원리를 보여준다.

Depth Anything은 대규모이면서 다양한 시각 학습(Large-Scale and Diverse Visual Learning)을 강조함으로써 단안 깊이 추정을 발전시킨다. 제한적으로 구성된 깊이 데이터셋에만 의존하는 대신 광범위한 영상 데이터와 강력한 사전학습 시각 표현(Pretrained Visual Representation)을 활용하여 다양한 장면에 대한 일반화 성능(Generalization)을 향상시키는 접근을 취한다. 실제 로봇 카메라는 지도 학습에 사용된 깊이 데이터셋과 크게 다른 환경, 객체, 조명 조건, 시점(Viewpoint), 질감을 경험할 수 있기 때문에 이러한 특성은 로봇 응용에서 특히 중요하다.

광범위한 데이터로 학습된 단안 깊이 모델의 중요한 장점은 이전에 보지 못한 환경에서도 유용한 기하학적 사전정보(Geometric Prior)를 제공할 수 있다는 것이다. 실내 복도, 창고, 도로, 식생, 기계, 사람, 다양한 객체에 대해 정확한 미터 깊이가 불확실한 경우에도 가까운 영역에서 먼 영역으로 이어지는 합리적인 공간 구조를 생성할 수 있다. 이러한 예측은 깊이 센서를 사용할 수 없거나 일시적으로 성능이 저하되거나 플랫폼의 크기, 비용, 소비전력, 감지 범위 등에 제약이 있을 때 기존 기하 센싱을 보완할 수 있다.

DPT와 Depth Anything을 단순히 서로 교체 가능한 깊이 센서(Depth Sensor)로 간주해서는 안 된다. 이들은 학습 데이터 분포(Training Distribution)와 시각적 증거에 따라 출력이 결정되는 학습 기반 추론 시스템(Learned Inference System)이다. 질감이 없는 표면, 거울, 투명 객체, 비정상적인 크기 관계, 강한 반사, 극단적인 조명, 모션 블러(Motion Blur), 익숙하지 않은 환경, 기하학적으로 모호한 객체에서는 잘못된 예측이 발생할 수 있다. 따라서 로봇은 불확실성을 유지해야 하며 단안 깊이를 절대적으로 신뢰할 수 있는 기하학적 측정으로 취급해서는 안 된다.

깊이가 신경망으로 예측되더라도 카메라 보정(Camera Calibration)은 중요하다. 내부 파라미터(Intrinsic Parameter)는 영상 픽셀과 카메라 광선(Camera Ray) 사이의 관계를 정의하며, 출력이 적절한 기하학적 의미를 가지는 경우 깊이 값을 3차원 좌표로 투영할 수 있게 한다. 보정된 초점 거리(Focal Length)와 주점(Principal Point)을 이용하면 픽셀과 깊이를 카메라 좌표계(Camera Coordinate)로 역투영(Back-Projection)할 수 있다. 외부 파라미터(Extrinsic Calibration)는 이러한 좌표를 로봇 본체, 매니퓰레이터, 지도 또는 다른 센서 좌표계와 연결한다.

신뢰할 수 있는 미터 깊이를 사용할 수 있다면 예측된 깊이 영상을 보정된 카메라 모델을 통해 픽셀 단위로 역투영하여 의사 포인트 클라우드(Pseudo Point Cloud)로 변환할 수 있다. 이러한 표현은 국부 장애물 분석(Local Obstacle Analysis), 장면 재구성(Scene Reconstruction), LiDAR 측정값과의 융합 등에 활용할 수 있다. 그러나 예측된 스케일의 오차는 그대로 3차원 위치 오차로 변환된다. 상대 깊이 모델은 물리적 거리가 필요한 계산에 사용하기 전에 스케일 정렬(Scale Alignment)이나 다른 미터 단위 정보가 필요하다.

시간적 일관성(Temporal Consistency)은 이동 로봇에서 또 다른 중요한 요구사항이다. 각 프레임을 독립적으로 처리하는 모델은 실제 장면이 부드럽게 변화하더라도 깊이 값이 프레임마다 변동할 수 있다. 카메라 움직임, 노출 변화, 모션 블러, 부분 가림, 작은 영상 변화로 인해 프레임 간 깊이 불안정성이 발생할 수 있다. 시간 필터링(Temporal Filtering), 시각 주행거리계(Visual Odometry), 광학 흐름(Optical Flow), 추적(Tracking), 다중 프레임 깊이 구조(Multi-Frame Depth Architecture)를 이용하면 이러한 불일치를 줄이고 내비게이션과 지도 작성에 보다 안정적인 표현을 제공할 수 있다.

단안 깊이는 의미 분할(Semantic Segmentation) 및 인스턴스 분할(Instance Segmentation)과 자연스럽게 결합할 수 있다. 분할은 어떤 픽셀이 객체 또는 환경 영역에 속하는지를 식별하고, 깊이는 해당 영역의 기하학적 배치를 추정한다. 따라서 로봇은 보행자, 팔레트, 차량, 바닥, 벽 등에 해당하는 깊이 값을 분리하여 대략적인 공간 관계를 계산할 수 있다. 이러한 결합은 순수한 영상 평면 기반 인식에서 객체 중심의 3차원 인지(Object-Aware 3D Perception)로 전환하는 기반을 제공한다.

LiDAR 또는 스테레오 센싱(Stereo Sensing)과의 융합은 단안 깊이 추정의 중요한 약점을 보완할 수 있다. LiDAR는 직접적인 거리 측정값을 제공하지만 일반적으로 카메라보다 영상 평면에서의 데이터 밀도가 낮으며, 학습 기반 단안 깊이는 밀집된 예측을 제공하지만 스케일이나 일반화 오차가 발생할 수 있다. 융합 시스템은 희소한 미터 단위 측정값(Sparse Metric Measurement)을 이용하여 밀집된 카메라 깊이를 제약하거나 보정함으로써 시각의 의미적 풍부함 및 공간적 밀도와 직접 거리 센싱의 기하학적 신뢰성을 결합할 수 있다.

내비게이션에서 깊이 지도는 자유 공간 추정(Free-Space Estimation), 장애물 검출, 지형 해석(Terrain Interpretation), 지역 비용 지도(Local Costmap) 생성에 활용될 수 있다. 가까운 영역을 로봇 좌표계로 투영한 뒤 예상되는 지면이나 주행 가능 통로와 비교하여 분석할 수 있다. 그러나 충돌의 결과가 중요한 시스템에서는 단안 깊이가 일반적으로 안전 등급 장애물 센싱(Safety-Rated Obstacle Sensing)을 대체하기보다는 보완해야 한다. 미확인 객체, 도메인 변화(Domain Shift), 잘못된 스케일 추정은 깊이 영상만으로 탐지하기 어려운 오류를 만들 수 있다.

조작에서는 밀집 깊이(Dense Depth)가 객체 검출이나 분할을 통해 식별된 목표 객체 주변의 기하 정보를 제공한다. 로봇은 표면 구조를 추정하고 전경과 배경을 분리하며 자세 추정(Pose Estimation)을 초기화하거나 파지 계획(Grasp Planning)을 위한 대략적인 3차원 영역을 생성할 수 있다. 조작 작업은 일반적인 장면 이해보다 높은 정확도를 요구하는 경우가 많으므로 단안 깊이 결과를 스테레오, RGB-D, 구조광(Structured Light), LiDAR, 다중 시점 재구성(Multi-View Reconstruction), 직접적인 기하학적 검증을 통해 보완할 수 있다.

평가(Evaluation)에서는 예측 품질과 실제 운용상의 유용성을 구분해야 한다. 일반적인 깊이 평가 지표는 절대 또는 상대 오차, 임계값 정확도(Threshold Accuracy), 로그 오차(Logarithmic Error), 스케일 불변 일치도(Scale-Invariant Agreement) 등을 기준 깊이와 비교한다. 로봇에서는 장애물 거리 오차, 경계 정확도, 시간적 안정성, 도메인 변화에서의 실패, 안전과 관련된 거리 범위에서의 성능도 추가로 평가해야 한다. 평균 벤치마크 성능이 우수한 모델도 얇은 장애물이나 반사면, 중요한 객체 경계에서는 허용할 수 없는 국부 오차를 발생시킬 수 있다.

입력 해상도(Input Resolution)와 모델 크기(Model Size)는 배포 과정에서 직접적인 절충 관계를 형성한다. 높은 해상도는 작은 장애물, 좁은 구조, 세밀한 깊이 불연속(Depth Discontinuity)을 보존하지만 GPU 연산량, 메모리 트래픽(Memory Traffic), 추론 지연시간을 증가시킨다. 대형 트랜스포머 인코더는 표현 품질을 향상시킬 수 있지만 엣지 컴퓨터(Edge Computer)의 실시간 처리 예산을 초과할 수 있다. 따라서 깊이 정확도와 함께 프레임 지연시간, GPU 메모리, 열적 특성(Thermal Behavior), 소비전력, 플랫폼의 요구 반응시간을 평가해야 한다.

엣지 배포(Edge Deployment)에서는 최적화된 런타임(Optimized Runtime)과 FP16 또는 충분히 검증된 경우 INT8과 같은 저정밀 연산(Reduced-Precision Inference)을 사용할 수 있다. 전처리, 텐서 전송(Tensor Transfer), 추론, 크기 조정, 깊이 정규화(Depth Normalization), 3차원 투영, ROS2 메시지 발행까지 모두 종단간 지연시간(End-to-End Latency) 측정에 포함해야 한다. 또한 역깊이(Inverse Depth)의 정규화나 크기 조정, 해석이 잘못되면 신경망 추론 자체가 정상적으로 동작하더라도 심각한 기하학적 오류가 발생할 수 있으므로 출력의 수학적 의미를 유지해야 한다.

ROS2 아키텍처에서 깊이 모듈은 보정된 카메라 영상(Calibrated Camera Image)을 구독하고 카메라 프레임 및 깊이 표현 방식을 설명하는 메타데이터(Metadata)와 함께 타임스탬프 기반 깊이 지도를 발행할 수 있다. 하위 노드(Downstream Node)는 깊이를 포인트 클라우드로 변환하고, 분할 마스크와 연결하거나, LiDAR와 융합하거나, 지역 공간 표현(Local Spatial Representation)을 구축할 수 있다. 로봇이 빠르게 이동하거나 다른 센서 측정값과 깊이를 결합하는 경우 원본 영상의 타임스탬프와 좌표 프레임 관계를 유지하는 것이 필수적이다.

단안 깊이 추정은 궁극적으로 일반적인 카메라 영상을 환경에 대한 학습 기반 기하 표현(Learned Geometric Representation)으로 변환한다. DPT는 트랜스포머 특징을 밀집 공간 예측으로 재구성하는 방법을 보여주며, Depth Anything은 광범위한 시각 학습과 일반화의 중요성을 보여준다. 카메라 인지(Camera Perception) 구조에서 단안 깊이는 2차원 객체 검출과 분할에서 스테레오 깊이(Stereo Depth), 조감도 인지(BEV Perception), 카메라 기반 3차원 객체 검출, 광학 흐름, 자세 추정, 다중 센서 융합(Multi-Sensor Fusion)으로 이어지는 자연스러운 연결 역할을 한다.

## 02.04. Stereo Depth Estimation SGBM RAFT Stereo [w/Code]

![](images/image4.png){width="7.268055555555556in" height="7.268055555555556in"}

스테레오 깊이 추정(Stereo Depth Estimation)은 알려진 공간적 간격을 가진 두 대의 카메라로 환경을 동시에 관측하여 장면의 기하 구조(Scene Geometry)를 복원한다. 학습된 시각 단서로 깊이를 추론하기 때문에 스케일 모호성(Scale Ambiguity)이 발생할 수 있는 단안 깊이 추정(Monocular Depth Estimation)과 달리, 보정된 스테레오 비전(Calibrated Stereo Vision)은 좌우 영상 사이의 기하학적 대응 관계(Geometric Correspondence)로부터 거리를 계산한다. 따라서 스테레오는 밀집된 3차원 인지가 필요한 이동 로봇, 자율주행 차량, 매니퓰레이터(Manipulator) 등에 중요한 수동형 거리 측정 기술(Passive Ranging Technology)이다.

스테레오 카메라 시스템(Stereo Camera System)은 일정한 베이스라인(Baseline)을 두고 배치된 좌측 및 우측 카메라로 구성된다. 두 카메라는 동일한 물리적 점을 서로 조금 다른 시점(Viewpoint)에서 관측하므로 해당 점의 영상 위치가 좌우 영상 사이에서 이동한다. 이러한 수평 방향의 변위를 시차(Disparity)라고 한다. 정류된 스테레오 영상(Rectified Stereo Pair)에서 깊이는 대략 Z = fB/d로 계산되며, Z는 깊이, f는 초점 거리(Focal Length), B는 베이스라인, d는 시차를 의미한다. 따라서 큰 시차는 가까운 객체를, 작은 시차는 먼 객체를 나타낸다.

정확한 보정(Calibration)은 스테레오 기하(Stereo Geometry)의 기본 조건이다. 내부 보정(Intrinsic Calibration)은 각 카메라의 초점 거리, 주점(Principal Point), 렌즈 왜곡(Lens Distortion)을 추정하고, 외부 보정(Extrinsic Calibration)은 두 카메라 사이의 회전(Rotation)과 이동(Translation)을 결정한다. 이후 스테레오 정류(Stereo Rectification)를 수행하여 대응점들이 거의 동일한 수평 주사선(Horizontal Scanline)에 위치하도록 두 영상을 변환한다. 이를 통해 2차원 대응점 탐색 문제를 에피폴라 선(Epipolar Line)을 따라 수행되는 주로 1차원적인 탐색 문제로 단순화할 수 있다.

핵심적인 계산 문제는 스테레오 정합(Stereo Matching), 즉 좌측 영상의 픽셀 또는 영상 특징에 대응하는 우측 영상의 픽셀이나 특징을 찾는 것이다. 정합에는 밝기(Intensity), 색상, 국부 질감(Local Texture), 학습된 특징(Learned Feature) 또는 이들을 결합한 표현을 사용할 수 있다. 대응 관계가 결정되면 시차를 계산하고 이를 깊이로 변환할 수 있다. 잘못된 대응 관계는 직접적으로 잘못된 기하 구조를 생성하므로 대응점 품질은 스테레오 인지 시스템의 신뢰성을 결정하는 가장 중요한 요소 중 하나이다.

스테레오 정합은 질감이 없는 영역(Textureless Region)에서 특히 어려워진다. 주변의 많은 픽셀이 거의 동일하게 보이기 때문에 올바른 대응점을 구별하기 어렵다. 반복 패턴(Repetitive Pattern)은 여러 개의 그럴듯한 대응 후보를 만들며, 반사면과 투명 표면은 좌우 영상의 외형이 일관되어야 한다는 가정을 위반한다. 또한 한쪽 카메라에서는 보이지만 다른 카메라에서는 보이지 않는 가림(Occlusion)이 발생할 수 있으며, 조명 차이, 모션 블러(Motion Blur), 영상 잡음, 부정확한 보정 역시 불안정하거나 잘못된 깊이 추정을 유발한다.

반전역 블록 정합(Semi-Global Block Matching, SGBM)은 국부 정합(Local Matching)의 효율성과 보다 넓은 공간적 일관성(Spatial Consistency)을 절충한 전통적인 스테레오 방법이다. 각 픽셀에서 독립적으로 시차를 선택하는 대신 정합 비용(Matching Cost)을 구성하고 여러 영상 방향을 따라 에너지 함수(Energy Function)를 근사적으로 최소화한다. 영상 정보가 급격한 변화를 지지하지 않는 경우 시차가 갑자기 변하는 것을 억제함으로써 단순한 국부 블록 정합보다 부드럽고 일관된 시차 지도를 생성하면서도 실시간 구현이 가능한 수준의 계산 효율성을 유지한다.

SGBM 파이프라인은 일반적으로 보정 및 정류된 스테레오 영상에서 시작하며, 사전에 정의된 시차 범위(Disparity Range)에 대해 정합 비용을 계산한다. 이후 여러 방향의 경로를 따라 비용을 집계하고, 평활성 페널티(Smoothness Penalty)를 적용하여 인접 픽셀이 유사한 시차를 가지도록 유도한다. 집계 비용이 가장 작은 시차를 선택한 뒤 일관성 검사(Consistency Check), 필터링, 정제(Refinement)를 추가할 수 있다. 최종 시차 영상은 보정된 초점 거리와 베이스라인을 이용하여 미터 단위 깊이(Metric Depth)로 변환된다.

여러 파라미터가 SGBM의 동작에 큰 영향을 준다. 최소 시차(Minimum Disparity)와 시차 개수는 탐색 가능한 깊이 범위를 결정하고, 블록 크기(Block Size)는 잡음 민감도와 세밀한 구조 보존 사이의 균형을 결정한다. 평활성 페널티는 인접 영역 사이에서 시차가 얼마나 쉽게 변화할 수 있는지를 조절한다. 유일성 제약(Uniqueness Constraint), 좌우 일관성 검사(Left-Right Consistency Check), 스펙클 필터링(Speckle Filtering), 서브픽셀 정제(Subpixel Refinement)는 신뢰성이 낮은 대응점을 제거하거나 정밀도를 향상시킬 수 있다.

SGBM은 기하학적 동작을 이해하기 쉽고 신경망 학습이 필요하지 않기 때문에 로봇 시스템에서 여전히 유용하다. 일반적인 CPU에서도 실행할 수 있으며, 제한된 시스템에서 유용한 결정론적 처리 특성(Deterministic Processing Characteristic)을 제공한다. 그러나 질감이 적은 표면, 얇은 구조, 반복 패턴, 큰 조명 변화, 복잡한 가림 환경에서는 기본 가정이 깨질 수 있다. 따라서 정확도는 영상 품질, 카메라 보정, 적절한 정합 파라미터 설정에 크게 의존한다.

RAFT-Stereo는 학습된 심층 시각 특징(Deep Visual Feature)과 반복적 정제(Iterative Refinement)를 이용하여 대응점 문제에 접근한다. 순환형 전쌍 필드 변환(Recurrent All-Pairs Field Transforms)의 개념을 기반으로 좌우 영상에서 특징 표현을 추출하고, 잠재적인 대응 관계를 나타내는 상관 정보(Correlation Information)를 구성한 뒤 순환 메커니즘(Recurrent Mechanism)을 통해 시차 추정치를 반복적으로 갱신한다. 즉시 하나의 국부 대응점을 결정하는 대신 학습된 문맥과 정합 증거를 이용하여 시차를 점진적으로 정제한다.

RAFT-Stereo의 핵심 개념 중 하나는 좌우 영상 특징 사이의 상관 표현(Correlation Representation)이다. 가능한 시차에 따른 특징 유사도(Feature Similarity)는 동일한 장면의 점이 상대 영상의 어느 위치에 존재할 가능성이 높은지를 나타낸다. 순환 갱신 연산자(Recurrent Update Operator)는 이러한 상관 정보와 문맥 특징(Contextual Feature), 현재의 시차 추정값을 반복적으로 참조한다. 각 반복 단계에서 보정값을 예측하고 시차 필드(Disparity Field)를 점차 일관된 해로 이동시킨다는 점에서 SGBM의 수작업 정합 비용 집계 방식과 근본적인 차이가 있다.

학습 기반 스테레오 모델(Learned Stereo Model)은 데이터에서 학습된 특징이 의미적 및 문맥적 정보를 포함하기 때문에 순수한 국부 알고리즘이 해결하기 어려운 모호성을 처리할 수 있다. 질감이 약한 표면에서도 고수준 시각 패턴이 대응 관계 판단에 도움을 줄 수 있으며 객체 경계와 주변 구조가 시차 정제를 지원할 수 있다. 그러나 RAFT-Stereo 역시 완전하지 않으며, 도메인 변화(Domain Shift), 비정상적인 광학 특성, 극단적인 날씨, 심각한 모션 블러, 학습되지 않은 카메라 구성, 반사 또는 투명 재질 등에서 성능이 저하될 수 있다.

따라서 SGBM과 RAFT-Stereo는 서로 보완적인 스테레오 접근 방식이다. SGBM은 명시적인 기하학적 가정, 설계된 정합 비용, 방향성 최적화(Directional Optimization), 설정 가능한 필터링을 사용한다. 반면 RAFT-Stereo는 데이터로부터 특징 표현과 반복적인 대응 관계 정제를 학습한다. SGBM은 해석하기 쉽고 CPU 중심 시스템에 배포하기 용이하지만, RAFT-Stereo는 일반적으로 더 많은 GPU 연산을 요구하는 대신 배포 환경이 학습 영역과 적절하게 일치한다면 어려운 시각 환경에서 더욱 강력한 시차 추정 성능을 제공할 수 있다.

스테레오 깊이 정확도는 베이스라인과 초점 거리에 크게 영향을 받는다. 넓은 베이스라인은 동일한 거리에서 더 큰 시차를 생성하여 장거리 깊이 해상도(Depth Resolution)를 향상시킬 수 있지만 두 카메라 사이의 시점 차이와 가림도 증가한다. 좁은 베이스라인은 근거리 장면에서 대응점 탐색을 쉽게 만들지만 장거리에서는 시차가 매우 작아진다. 따라서 카메라 선택과 기계적 배치는 응용 분야에서 요구되는 감지 범위(Sensing Range)를 기준으로 설계해야 한다.

시차가 작아질수록 깊이 불확실성(Depth Uncertainty)도 빠르게 증가한다. 깊이는 시차에 반비례하기 때문에 장거리에서 작은 시차 오차도 큰 거리 오차를 발생시킬 수 있다. 따라서 스테레오 시스템은 초점 거리, 베이스라인, 영상 해상도, 정합 정밀도로 결정되는 제한된 유효 작동 범위(Working Range)에서 가장 높은 성능을 발휘한다. 내비게이션과 장애물 검출에서는 전체 시야에서 깊이 정확도가 일정하다고 가정하기보다 거리에 따른 깊이 오차 특성을 분석해야 한다.

스테레오 깊이는 자연스럽게 밀집된 3차원 정보(Dense 3D Information)를 생성할 수 있다. 시차를 미터 단위 깊이로 변환한 뒤 보정된 픽셀을 카메라 좌표계로 역투영(Back-Projection)하면 포인트 클라우드(Point Cloud)를 생성할 수 있다. 이후 의미 분할(Semantic Segmentation)이나 인스턴스 분할(Instance Segmentation) 마스크를 이용하여 3차원 점들을 보행자, 차량, 팔레트, 바닥, 벽, 조작 대상과 연결할 수 있다. 이를 통해 카메라 기반 객체 인식과 명시적인 기하 구조를 결합하여 의미 포인트 클라우드(Semantic Point Cloud), 객체 거리 추정, 지역 지도 작성(Local Mapping), 기하학적 추론을 수행할 수 있다.

시간적 처리(Temporal Processing)는 이동 로봇에서 스테레오 인지 성능을 향상시킬 수 있다. 독립적으로 생성된 시차 지도는 영상 잡음, 질감 변화, 진동, 일시적인 대응 실패로 인해 프레임 사이에서 변동할 수 있다. 시각 주행거리계(Visual Odometry), 광학 흐름(Optical Flow), 추적(Tracking), 시간 필터링(Temporal Filtering)을 사용하면 여러 프레임의 정보를 정렬하고 불안정한 측정값을 억제할 수 있다. 그러나 움직이는 객체에 정적 기하 구조를 잘못 가정하면 깊이 관측값이 번지거나 지도와 장애물 표현에 고스트 구조(Ghost Structure)가 생성될 수 있으므로 주의해야 한다.

스테레오는 LiDAR, 레이더(Radar), 관성측정장치(IMU), 단안 깊이 추정과도 융합할 수 있다. LiDAR는 정확한 희소 또는 준밀집 거리 측정값을 제공하고, 레이더는 악천후에서도 강인한 거리와 속도 정보를 제공하며, IMU는 플랫폼의 운동 상태를 파악하는 데 도움을 준다. 학습 기반 단안 깊이는 스테레오 대응 관계가 약한 영역에서 유용한 사전정보(Prior)를 제공할 수 있다. 센서 융합은 하나의 카메라 쌍이 모든 환경 조건에서 신뢰성을 유지해야 한다는 부담을 줄이고 각 센서의 상호 보완적인 특성을 활용한다.

내비게이션에서 스테레오 깊이는 주변 장애물 식별, 자유 공간(Free Space) 추정, 지형 분석(Terrain Analysis), 지역 점유 지도(Local Occupancy Map) 또는 비용 지도(Costmap) 생성에 활용할 수 있다. 조작에서는 분할된 객체 주변의 밀집 깊이 정보를 이용하여 표면 재구성, 자세 추정(Pose Estimation), 파지 계획(Grasp Planning), 충돌 검사를 수행할 수 있다. 스테레오는 적외선 패턴과 같은 능동 조명(Active Illumination)을 투사할 필요가 없는 수동형 센싱이라는 장점이 있지만, 가시 영상의 질감에 의존한다는 점에서 능동형 깊이 카메라와 다른 제약을 가진다.

평가(Evaluation)에는 시차 오차(Disparity Error), 깊이 오차(Depth Error), 유효하지 않은 픽셀 비율(Invalid-Pixel Rate), 경계 품질(Boundary Quality), 거리에 따른 성능을 포함해야 한다. 로봇 시스템에서는 추가적으로 시간적 안정성, 얇은 장애물 검출, 지연시간(Latency), 조명 변화에서의 실패, 보정 민감도(Calibration Sensitivity), 반사면이나 저질감 표면에서의 동작을 평가해야 한다. 평균적인 벤치마크 정확도만으로 실제 운용 적합성을 판단할 수 없으며, 장애물 주변에서 발생하는 소수의 심각한 깊이 오류가 중요하지 않은 배경 영역의 작은 오차보다 훨씬 큰 영향을 미칠 수 있다.

실시간 배포(Real-Time Deployment)에서는 전체 스테레오 파이프라인을 고려해야 한다. 카메라 동기화(Camera Synchronization), 영상 획득, 정류, 정합 또는 신경망 추론, 시차 필터링, 깊이 변환, 포인트 클라우드 생성, ROS2 통신이 모두 지연시간에 영향을 준다. SGBM은 CPU나 임베디드 프로세서(Embedded Processor)에서도 실용적일 수 있지만, RAFT-Stereo는 일반적으로 GPU 가속과 저정밀 추론(Reduced-Precision Inference)의 이점을 크게 얻는다. 해상도, 시차 범위, 모델 크기, 메모리 사용량, 열적 한계(Thermal Limit), 요구 갱신 주기를 기하학적 정확도와 함께 균형 있게 설계해야 한다.

ROS2 아키텍처에서는 동기화된 좌우 카메라 스트림(Camera Stream)이 보정 정보와 함께 영상 획득 타임스탬프(Acquisition Timestamp)를 유지해야 한다. 정류된 영상은 전통적인 SGBM 또는 학습 기반 RAFT-Stereo 노드에 입력될 수 있으며, 표준화된 깊이 또는 시차 출력 인터페이스를 사용하면 하위 소프트웨어가 선택된 알고리즘에 크게 의존하지 않도록 구성할 수 있다. 이후 포인트 클라우드 생성, 분할 융합, 장애물 검출, 지도 작성, 내비게이션 모듈이 동일한 기하학적 인터페이스를 사용할 수 있으므로 스테레오 알고리즘을 모듈 방식으로 교체하고 비교 평가할 수 있다.

스테레오 깊이 추정은 궁극적으로 두 카메라의 관측을 대응 관계(Correspondence)와 삼각측량(Triangulation)을 통해 명시적인 미터 단위 기하 구조로 변환한다. SGBM은 설계된 정합 비용과 반전역 최적화(Semi-Global Optimization)를 통해 실용적인 시차 지도를 생성하는 방법을 보여주며, RAFT-Stereo는 학습된 특징, 상관 표현, 반복적 정제의 강점을 보여준다. 카메라 인지(Camera Perception) 구조에서 스테레오 깊이는 단안 깊이를 보완하면서 조감도 인지(BEV Perception), 3차원 객체 검출(3D Object Detection), 광학 흐름, 자세 추정, 이후의 다중 센서 융합(Multi-Sensor Fusion)을 위한 기하학적 기반을 제공한다.

## 02.05. BEV Perception from Multi Camera BEVFormer [w/Code]

![](images/image5.png){width="7.268055555555556in" height="7.268055555555556in"}

조감도 인지(Bird's-Eye-View Perception)는 주변에 배치된 여러 카메라의 관측 정보를 로봇 또는 차량 좌표계(Robot or Vehicle Coordinate System)에 정의된 하나의 통합 표현으로 변환한다. 각 원근 영상(Perspective Image)을 독립적으로 해석하는 대신, 시스템은 시각 정보를 공통의 지면 중심 공간 평면(Ground-Oriented Spatial Plane)에 구성한다. 이러한 표현은 내비게이션(Navigation), 장애물 판단, 궤적 계획(Trajectory Planning), 지도 작성(Mapping), 이동 객체와의 상호작용을 플랫폼 주변의 미터 단위 공간에서 자연스럽게 표현할 수 있기 때문에 자율 로봇에 특히 유용하다.

다중 카메라 시스템(Multi-Camera System)은 일반적으로 로봇 주변에 여러 카메라를 배치하여 서로 중첩되거나 상호 보완적인 시야(Field of View)를 제공한다. 전방, 후방, 측면 카메라는 환경의 서로 다른 영역을 관측하며 각 영상에는 카메라 광학 기하(Optical Geometry)에 의해 결정되는 원근 왜곡(Perspective Distortion)이 포함된다. 카메라에 가까운 객체는 크게 보이고 먼 객체는 작게 보인다. BEV 인지는 이러한 서로 다른 원근 관측을 공통 공간 표현으로 변환하여 하위 경로 계획 모듈이 위치와 공간 관계를 보다 쉽게 해석하도록 한다.

카메라 보정(Camera Calibration)은 이러한 변환의 기본 요소이다. 각 카메라는 초점 거리(Focal Length), 주점(Principal Point), 렌즈 왜곡(Lens Distortion)을 설명하는 내부 파라미터(Intrinsic Parameter)와 로봇 좌표계에 대한 위치 및 방향을 정의하는 외부 파라미터(Extrinsic Parameter)를 필요로 한다. 이러한 파라미터는 영상 픽셀과 3차원 시선 광선(Viewing Ray) 사이의 기하학적 관계를 정의한다. 정확한 보정은 여러 카메라의 시각 특징을 일관된 공간 영역에 연결하지만, 보정 오차는 BEV 특징의 위치 이동, 중복 또는 기하학적 불일치를 발생시킬 수 있다.

전통적인 접근법은 역원근 매핑(Inverse Perspective Mapping)을 이용하여 영상 정보를 지면 평면(Ground Plane)에 투영할 수 있다. 이러한 방법은 관련 표면이 대략적인 평면 지면 가정(Planar-Ground Assumption)을 만족할 때 효과적이지만 실제 환경에는 차량, 보행자, 연석, 벽, 경사로, 식생, 기계류와 같이 지면 위 또는 아래로 확장되는 구조물이 존재한다. 따라서 현대의 학습 기반 BEV 아키텍처(Learned BEV Architecture)는 고정된 기하학적 변환으로 영상 픽셀을 단순히 워핑(Warping)하는 대신 더욱 풍부한 공간 표현을 추론하는 것을 목표로 한다.

BEVFormer는 어텐션(Attention)을 이용하여 다중 카메라 영상으로부터 BEV 특징을 구성하는 트랜스포머 기반 아키텍처(Transformer-Based Architecture)로 이 문제에 접근한다. 미리 정의된 BEV 쿼리(BEV Query) 격자는 자차 플랫폼(Ego Platform) 주변의 공간 위치를 나타낸다. 이 쿼리들은 각 카메라 영상을 직접 조감도 평면으로 재표본화(Resampling)하는 대신 주변 카메라에서 추출된 다중 스케일 영상 특징(Multi-Scale Image Feature)과 상호작용한다. 결과적으로 카메라 기하와 학습된 시각 정보를 결합하면서 BEV 좌표계에서 일관된 공간 구조를 유지한다.

처리 파이프라인은 시각 백본(Visual Backbone)을 이용하여 모든 카메라에서 계층적 영상 특징(Hierarchical Image Feature)을 추출하는 단계에서 시작한다. 가까운 객체는 영상의 큰 영역을 차지하지만 멀리 있는 차량, 보행자, 장애물은 매우 작게 나타날 수 있기 때문에 다중 스케일 특징이 중요하다. 네트워크는 세밀한 공간 정보를 보존하면서 동시에 고수준 의미 문맥(Semantic Context)을 학습해야 한다. 이후 모든 카메라의 특징은 BEV 쿼리가 환경의 대응 공간 영역에 대한 정보를 수집하는 시각적 정보원으로 사용된다.

BEV 쿼리는 플랫폼 주변의 미리 정의된 영역에 분포된 잠재 공간 셀(Latent Spatial Cell)로 이해할 수 있다. 각 쿼리는 특정 영상 픽셀이 아니라 하향식 좌표계(Top-Down Coordinate System)의 공간 위치를 나타낸다. 카메라 보정 정보는 해당 공간 가설(Spatial Hypothesis)이 사용 가능한 카메라 영상의 어느 위치에 나타날 수 있는지를 결정하는 관계를 제공한다. 어텐션 기반 샘플링(Attention-Based Sampling)을 통해 각 BEV 쿼리는 관련 시각 정보를 수집하고 자신의 표현을 갱신하면서 원근 영상 특징을 구조화된 하향식 특징 맵으로 변환한다.

공간 교차 어텐션(Spatial Cross-Attention)은 BEVFormer의 핵심 구성요소이다. 하나의 BEV 쿼리가 모든 카메라의 모든 픽셀과 동일하게 상호작용할 필요는 없다. 대신 기하학적 투영(Geometric Projection)을 통해 해당 쿼리가 나타내는 공간 위치와 관련될 가능성이 있는 카메라 영역을 식별한다. 이후 어텐션은 이러한 영역과 카메라에서 유용한 특징을 선택적으로 샘플링한다. 이러한 기하 기반 메커니즘은 불필요한 상호작용을 줄이고 다중 시점 원근 특징과 공통 BEV 표현 사이에 구조화된 연결을 제공한다.

다중 카메라의 시야 중첩(Multi-Camera Overlap)은 기회와 문제를 동시에 제공한다. 하나의 물리적 객체가 여러 카메라에 동시에 나타날 수 있으므로 상호 보완적인 관측을 이용하여 강인성을 향상시킬 수 있다. 그러나 시점, 가림(Occlusion), 조명, 렌즈 특성, 특징 해상도의 차이로 인해 관측 결과가 서로 일치하지 않을 수 있다. BEV 표현은 반복적으로 관측된 동일 객체를 서로 다른 물리적 객체로 해석하지 않으면서 여러 시점을 통합해야 한다. 따라서 정확한 외부 보정과 학습 기반 다중 시점 특징 집계(Multi-View Feature Aggregation)가 공간적 일관성을 유지하는 데 필수적이다.

BEVFormer는 로봇이 독립된 정지 영상이 아니라 지속적으로 변화하는 세계에서 동작한다는 점을 고려하여 시간 정보(Temporal Information)도 통합한다. 이전 프레임의 BEV 표현은 이미 관측된 객체와 구조에 대한 유용한 정보를 포함한다. 시간 자기 어텐션(Temporal Self-Attention)은 현재의 BEV 쿼리가 과거의 BEV 특징과 상호작용하도록 한다. 융합 전에 자기 운동(Ego-Motion)을 고려하여 이전 공간 정보를 현재 로봇 좌표계에 정렬해야 하며, 그렇지 않으면 과거 위치의 정보가 현재 공간에 잘못 중첩될 수 있다.

시간 융합(Temporal Fusion)은 여러 가지 이점을 제공한다. 현재 영상에서 일시적으로 가려지거나 명확하게 보이지 않는 객체도 이전 관측의 유용한 정보를 유지할 수 있다. 멀리 있거나 작은 객체는 여러 프레임에서 증거를 누적할 수 있으며 정적인 환경 구조는 더욱 안정적으로 표현될 수 있다. 따라서 시간적 추론(Temporal Reasoning)은 프레임 간 변동을 줄이고 연속성을 향상시킬 수 있다. 그러나 이동 객체는 자차 플랫폼과 독립적으로 위치가 변화하므로 자기 운동 보상(Ego-Motion Compensation)만으로 처리할 수 없으며 별도의 주의가 필요하다.

생성된 BEV 특징 맵(BEV Feature Map)은 반드시 일반적인 영상 형태일 필요는 없다. 각 공간 셀이 하위 인지 작업에 필요한 정보를 인코딩하는 학습된 잠재 표현(Learned Latent Representation)이다. 검출 헤드(Detection Head)는 이 표현으로부터 객체 클래스, 위치, 크기, 방향, 속도 등을 예측할 수 있다. 다른 헤드는 의미 BEV 분할(Semantic BEV Segmentation), 차선 또는 주행 가능 영역 추정, 점유 예측(Occupancy Prediction), 지도 요소 추출(Map-Element Extraction)을 수행할 수 있다. 따라서 하나의 공유 BEV 백본이 공통 좌표 체계를 이용하여 여러 공간 인지 작업을 지원할 수 있다.

3차원 객체 검출(3D Object Detection)은 BEV 인지의 가장 중요한 응용 중 하나이다. 원근 영상에서는 겉보기 크기가 깊이에 크게 의존하므로 객체 사이의 미터 단위 공간 관계를 직접 추론하기 어렵다. BEV 공간에서는 로봇을 기준으로 종방향(Longitudinal)과 횡방향(Lateral) 좌표를 이용하여 객체를 표현할 수 있다. 예측된 3차원 경계 상자(3D Bounding Box)는 중심 위치, 크기, 방향, 클래스, 잠재적으로 운동 상태까지 설명할 수 있어 객체 추적, 움직임 예측(Motion Prediction), 경로 계획 모듈로 자연스럽게 전달할 수 있다.

BEV 분할(BEV Segmentation)은 또 다른 중요한 기능을 제공한다. 시스템은 학습 목적에 따라 공간 셀을 도로, 주행 가능 표면(Drivable Surface), 차선 영역, 장애물, 식생, 보도 등의 의미 카테고리로 분류할 수 있다. 가시 픽셀을 설명하는 영상 분할(Image Segmentation)과 달리 BEV 분할은 로봇 주변의 공간을 직접 의미적으로 구성한다. 따라서 하위 모듈이 궤적 생성에 사용하는 공간 좌표계와 가까운 형태로 데이터를 제공하므로 의미 지도(Semantic Map)와 내비게이션 비용 표현(Navigation Cost Representation)을 구성하기 용이하다.

카메라 기반 BEV 인지의 중요한 장점은 밀집 능동 거리 센싱(Dense Active Range Sensing)에 의존하지 않고도 풍부한 공간 이해를 생성할 수 있다는 것이다. 카메라는 비교적 낮은 센서 비용으로 높은 해상도의 색상, 질감, 의미 정보를 제공한다. 그러나 카메라로 추론된 BEV 기하 구조는 여전히 깊이 모호성(Depth Ambiguity), 가림, 불량한 조명, 악천후, 도메인 변화(Domain Shift)에 취약하다. 따라서 출력이 미터 단위처럼 보이는 하향식 좌표계로 표현된다는 이유만으로 이를 직접적인 물리적 측정값으로 간주해서는 안 된다.

보정 민감도(Calibration Sensitivity)는 다중 카메라 BEV 시스템에서 특히 중요하다. 카메라 방향의 작은 오차도 장거리에서는 상당한 공간 위치 오차로 확대될 수 있다. 기계적 진동, 열적 영향(Thermal Effect), 카메라 교체, 장착 위치 변화는 기존에 측정된 외부 파라미터를 무효화할 수 있다. 따라서 실제 운용 로봇에서는 보정 안정성을 모니터링하고 검증 또는 재보정(Recalibration) 절차를 제공해야 한다. 현실적인 보정 오차를 포함하여 학습하면 강인성을 향상시킬 수 있지만 올바른 물리적 보정 자체의 필요성을 제거할 수는 없다.

BEV 격자의 공간 범위(Spatial Extent)와 해상도는 중요한 공학적 절충 관계(Engineering Trade-Off)를 형성한다. 넓은 영역은 로봇이 먼 객체까지 추론할 수 있게 하지만 BEV 쿼리 수와 계산량을 증가시킨다. 더 작은 크기의 셀은 높은 공간 정밀도를 제공하지만 더 많은 메모리와 처리 시간을 요구한다. 따라서 격자는 벤치마크 설정만을 기준으로 선택하기보다 플랫폼 속도, 제동 거리(Braking Distance), 카메라 커버리지(Camera Coverage), 요구 검출 거리, 계획 범위(Planning Horizon), 사용 가능한 엣지 컴퓨팅 자원을 고려하여 설계해야 한다.

다중 카메라 BEV 처리는 여러 개의 고해상도 영상 스트림을 동시에 처리해야 하기 때문에 계산량이 크다. 백본 추론, 다중 스케일 특징 저장, 어텐션 연산, 시간 특징 관리, 작업별 예측 헤드가 모두 GPU 메모리와 대역폭을 사용한다. 따라서 실시간 배포(Real-Time Deployment)에서는 영상 해상도, 카메라 수, 백본 크기, BEV 격자 크기, 수치 정밀도(Numerical Precision), 추론 런타임(Inference Runtime)을 신중하게 선택해야 한다. 버퍼링(Buffering)으로 인해 BEV 표현이 과거의 세계 상태를 나타낸다면 높은 처리량(Throughput)만으로는 충분하지 않다.

동기화(Synchronization) 역시 중요하다. 특히 로봇이나 주변 객체가 빠르게 움직일 때 여러 카메라 영상은 가능한 한 동일한 물리적 시점에 대응해야 한다. 동기화되지 않은 영상은 서로 다른 객체 위치를 나타낼 수 있으며 다중 시점 특징 집계 과정에서 오류를 발생시킨다. 하드웨어 트리거(Hardware Trigger), 정확한 타임스탬프(Timestamp), 결정론적 데이터 전송(Deterministic Transport), 알려진 시간 오프셋(Time Offset)의 보상을 통해 일관성을 향상시킬 수 있다. 시간적 BEV 아키텍처는 연속된 시간 단계 사이의 표현을 정렬하기 위해 신뢰할 수 있는 자기 운동 추정(Ego-Motion Estimation)도 필요로 한다.

센서 융합(Sensor Fusion)은 카메라 기반 BEV 인지를 더욱 강화할 수 있다. LiDAR는 직접적인 기하학적 측정값을 제공하고, 레이더(Radar)는 악천후에서도 강인한 거리 및 상대 속도 정보를 제공하며, GNSS와 관성측정장치(IMU)는 자기 운동 정보를 제공한다. 휠 오도메트리(Wheel Odometry)는 단기간의 운동 추정을 지원할 수 있다. 이러한 측정값은 아키텍처에 따라 BEV 특징 구성 이전, 구성 과정 또는 이후에 융합할 수 있으며, 공통의 하향식 좌표계는 서로 다른 센서 관측을 통합하기 위한 자연스러운 인터페이스를 제공한다.

이동 로봇과 실외 자율이동로봇(Outdoor AMR)에서 BEV 인지는 장애물 검출, 주행 가능성 분석(Traversability Analysis), 지역 지도 작성(Local Mapping), 궤적 계획, 보행자 또는 차량과의 상호작용을 지원할 수 있다. 자율주행에서는 동일한 표현으로 차선, 주변 이동 객체, 도로 경계, 동적 교통 구조를 표현할 수 있다. 실내 로봇은 복도와 작업 공간에 적합하도록 더 작고 세밀한 BEV 영역을 사용할 수 있다. 핵심 원리는 동일하며, 원근 카메라 관측을 자율 플랫폼 중심의 공간 표현으로 재구성하는 것이다.

평가(Evaluation)는 객체 검출 정확도만을 고려해서는 안 된다. 공간 위치 오차(Spatial Localization Error), 방향 오차(Orientation Error), BEV 분할 품질, 시간적 안정성, 개별 카메라 장애, 보정 민감도, 장거리 성능, 지연시간(Latency)이 실제 운용상의 유용성에 영향을 준다. 또한 다중 카메라 시스템은 일부 카메라가 가려지거나 고장난 상태에서도 제한적으로 동작할 수 있으므로 이러한 상황에서의 강인성도 평가해야 한다. 인지 품질이 어떻게 저하되는지를 이해하는 것은 진단(Diagnostics), 폴백 동작(Fallback Behavior), 안전 감독(Safety Supervision)을 설계하는 데 중요하다.

ROS2 아키텍처에서는 개별 카메라 스트림, 보정 데이터, 타임스탬프, 자기 운동 정보를 BEV 인지 노드 또는 분산 처리 파이프라인(Distributed Processing Pipeline)에 입력할 수 있다. 결과 출력에는 BEV 특징 맵, 3차원 검출 결과, 의미 격자(Semantic Grid), 점유 정보(Occupancy Information), 추적 객체(Tracked Object) 등이 포함될 수 있다. 일관된 좌표계 규칙(Coordinate Convention)과 정확한 시간 관계를 유지하면 내비게이션, 지도 작성, 객체 추적, 움직임 예측, 경로 계획 모듈이 내부 트랜스포머 구조에 직접 의존하지 않고 BEV 결과를 사용할 수 있다.

BEVFormer는 다중 카메라 인지가 개별 원근 영상 분석에서 통합된 공간 추론(Unified Spatial Reasoning)으로 발전할 수 있는 방법을 보여준다. BEV 쿼리, 기하 기반 공간 교차 어텐션(Geometry-Guided Spatial Cross-Attention), 시간 자기 어텐션, 다중 카메라 특징을 결합하여 자율 플랫폼 주변에 지속적인 하향식 표현을 생성한다. 카메라 인지(Camera Perception) 구조에서 이러한 방식은 2차원 객체 검출, 분할, 깊이 추정에서 카메라 기반 3차원 객체 검출, 시간적 장면 이해(Temporal Scene Understanding), 지도 작성, 내비게이션, 이후의 다중 센서 융합(Multi-Sensor Fusion)으로 이어지는 연결 역할을 한다.

## 02.06. Camera Based 3D Object Detection FCOS3D [w/Code]

![](images/image6.png){width="7.268055555555556in" height="7.268055555555556in"}

카메라 기반 3차원 객체 검출(Camera-Based 3D Object Detection)은 기존의 영상 객체 검출을 확장하여 카메라 관측으로부터 객체의 위치, 물리적 크기, 방향을 3차원 공간에서 추정한다. 단순히 2차원 경계 상자(2D Bounding Box)만 반환하는 대신, 검출기는 로봇 좌표계(Robot Coordinate System)와 연결할 수 있는 3차원 표현을 예측한다. 이를 통해 카메라 인지는 내비게이션(Navigation), 객체 추적(Tracking), 충돌 회피(Collision Avoidance), 움직임 예측(Motion Prediction), 주변 객체와의 상호작용에 필요한 공간적 추론(Spatial Reasoning)을 지원할 수 있다.

핵심적인 어려움은 원근 영상(Perspective Image)이 미터 단위 깊이(Metric Depth)를 직접 포함하지 않는다는 점이다. 서로 다른 여러 3차원 구성이 유사한 2차원 투영(2D Projection)을 생성할 수 있으므로 검출기는 시각적 증거로부터 깊이와 물리적 스케일을 추론해야 한다. 원근법(Perspective), 객체의 겉보기 크기, 가림(Occlusion), 질감(Texture), 의미적 클래스(Semantic Category), 지면과의 관계, 학습된 사전정보(Learned Prior) 등이 유용한 정보를 제공한다. 따라서 카메라 기반 3차원 검출은 2차원 검출이나 직접 거리 센싱(Direct Range Sensing)보다 근본적으로 더 큰 모호성을 가진다.

3차원 경계 상자(3D Bounding Box)는 일반적으로 객체의 중심 위치(Center Position), 물리적 크기(Physical Dimensions), 방향(Orientation)을 이용하여 객체를 표현한다. 좌표계 규칙(Coordinate Convention)에 따라 중심은 카메라 또는 로봇을 기준으로 횡방향, 수직방향, 종방향 좌표로 표현할 수 있다. 크기는 일반적으로 너비, 높이, 길이를 나타내며 방향은 객체의 헤딩(Heading) 또는 요 각도(Yaw Angle)를 표현한다. 검출기 구조와 응용 분야에 따라 클래스 확률, 신뢰도, 영상에 투영된 중심점, 속도 또는 불확실성(Uncertainty)이 추가 출력으로 포함될 수 있다.

FCOS3D는 FCOS의 앵커 프리(Anchor-Free) 철학을 단안 3차원 객체 검출(Monocular 3D Object Detection)에 적용한다. 미리 정의된 2차원 또는 3차원 앵커 상자(Anchor Box)에 의존하지 않고 다중 스케일 영상 특징 맵(Multi-Scale Image Feature Map)의 위치에서 객체 특성을 직접 예측한다. 따라서 서로 다른 크기, 종횡비(Aspect Ratio), 방향, 깊이를 가진 많은 앵커를 설계하고 정합할 필요가 없다. 앵커 프리 방식은 영상 특징과 3차원 객체를 설명하는 데 필요한 파라미터 사이에 비교적 직접적인 연결을 제공한다.

처리 파이프라인은 카메라 영상을 합성곱 백본(Convolutional Backbone)과 다중 스케일 특징 피라미드(Multi-Scale Feature Pyramid)에 입력하는 단계에서 시작한다. 백본은 점차 의미적인 시각 표현을 추출하고, 특징 피라미드는 여러 공간 해상도에서 정보를 보존한다. 따라서 가까이에 있는 큰 객체와 멀리 있는 작은 객체가 서로 다른 특징 계층(Feature Level)을 활성화할 수 있다. 각 특징 맵 위치는 잠재적인 예측 지점(Prediction Point)이 되며, FCOS3D는 이 위치에서 클래스 신뢰도와 3차원 객체 후보의 여러 기하학적 속성을 추정한다.

FCOS3D는 기존의 2차원 경계 상자 중심에만 의존하는 대신 투영된 3차원 중심(Projected 3D Center)을 객체와 연결한다. 물리적 객체는 3차원 공간에 중심점을 가지며 카메라 투영(Camera Projection)은 이 중심을 영상 평면으로 매핑한다. 검출기는 특징 맵 위치와 이러한 투영 중심 사이의 관계를 나타내는 오프셋(Offset)을 학습한다. 이를 통해 밀집 앵커 프리 검출(Dense Anchor-Free Detection)의 계산적 장점을 유지하면서 깊이, 크기, 방향을 예측하기 위한 기하학적으로 의미 있는 기준점을 제공한다.

깊이 예측(Depth Prediction)은 시선 방향(Viewing Direction)의 오차가 재구성된 3차원 위치에 직접적인 영향을 주기 때문에 가장 중요한 구성요소 중 하나이다. 영상 공간에서 작은 오차라도 먼 객체에서는 큰 미터 단위 위치 오차로 이어질 수 있다. 네트워크는 객체 외형과 문맥적 단서(Contextual Cue)를 이용하여 깊이를 학습하지만 이러한 추정은 학습 데이터 분포(Training Distribution)에 의존한다. 비정상적인 크기의 객체, 익숙하지 않은 시점(Viewpoint), 심한 가림, 특정 도메인에 특화된 외형은 2차원 검출이 시각적으로 타당하더라도 잘못된 깊이 값을 생성할 수 있다.

물리적 크기(Physical Dimensions)는 3차원 정보를 추정하기 위한 또 다른 중요한 정보원이다. 객체 클래스는 일반적으로 보행자, 승용차, 트럭과 같이 통계적인 크기 규칙을 가진다. FCOS3D는 이러한 사전정보를 학습하고 영상 특징으로부터 객체의 크기를 예측할 수 있다. 그러나 클래스 수준의 사전정보(Category-Level Prior)를 직접적인 물리 측정값과 동일하게 간주해서는 안 된다. 산업용 로봇은 일반적인 자율주행 데이터셋에 포함된 객체와 상당히 다른 크기를 가진 맞춤형 카트, 기계, 컨테이너, 적재물을 관측할 수 있다.

방향 추정(Orientation Estimation)은 객체가 카메라 또는 기준 좌표계에 대해 어떻게 회전되어 있는지를 결정한다. 단안 영상에서의 방향 추정은 서로 다른 헤딩이 유사한 외형으로 나타날 수 있기 때문에 어렵고, 특히 대칭에 가까운 객체에서 이러한 문제가 두드러진다. FCOS3D는 방향 정보를 보다 안정적으로 추정할 수 있도록 방향 예측을 분해하여 처리한다. 정확한 헤딩은 차량 추적, 궤적 예측(Trajectory Prediction), 조작(Manipulation), 공간적 상호작용에서 특히 중요하다.

다중 스케일 예측(Multi-Scale Prediction)은 원근 투영으로 인해 객체의 겉보기 크기가 거리에 따라 크게 변화하기 때문에 필수적이다. 가까운 차량은 수백 개의 픽셀을 차지할 수 있지만 동일한 차량이 멀리 있으면 매우 작은 영역으로 나타난다. 특징 피라미드 네트워크(Feature Pyramid Network)는 여러 해상도를 제공하여 서로 다른 크기의 객체를 적절한 특징 계층에 할당할 수 있도록 한다. 그러나 매우 먼 객체는 영상 해상도가 제한되면서 의미적 정보와 신뢰할 수 있는 깊이 추정에 필요한 기하학적 단서가 모두 감소하므로 여전히 검출하기 어렵다.

카메라 보정(Camera Calibration)은 FCOS3D의 예측 결과를 물리적 기하 구조와 연결한다. 내부 파라미터(Intrinsic Parameter)는 픽셀과 카메라 광선(Camera Ray) 사이의 관계를 정의하고, 외부 파라미터(Extrinsic Parameter)는 위치를 카메라 좌표계에서 로봇, 차량 또는 월드 좌표계(World Coordinate System)로 변환한다. 따라서 보정은 단순한 전처리 메타데이터가 아니라 영상 기반 예측을 실제 공간 관측으로 변환하는 핵심 요소이다. 초점 거리, 카메라 방향 또는 장착 위치의 오차는 신경망 출력이 일관적이더라도 객체 위치를 체계적으로 왜곡할 수 있다.

카메라 좌표계(Camera Coordinate System)와 로봇 좌표계(Robot Coordinate System)의 차이는 하위 시스템 통합에서 중요하다. 검출기는 일반적으로 광학 카메라 좌표계(Optical Camera Frame)를 기준으로 객체 위치를 예측할 수 있지만 내비게이션과 경로 계획(Planning)은 일반적으로 로봇 본체 중심 좌표계(Body-Centered Coordinate System) 또는 지도 좌표계(Map Coordinate System)에서 동작한다. 보정된 강체 변환(Rigid Transformation)을 통해 3차원 검출 결과를 필요한 좌표계로 변환할 수 있다. 명확한 좌표 규칙을 유지하면 추적, 점유 지도(Occupancy Mapping), 충돌 평가로 전파될 수 있는 축 방향, 부호, 방향 오류를 방지할 수 있다.

FCOS3D와 BEVFormer는 모두 카메라 기반 3차원 인지를 지원하지만 개념적인 접근 방식은 다르다. FCOS3D는 주로 원근 영상 특징(Perspective Image Feature)에서 직접 3차원 객체 특성을 예측하는 반면, BEVFormer는 여러 카메라로부터 공유 조감도 표현(Bird's-Eye-View Representation)을 먼저 구성한 뒤 공간 예측을 수행한다. 단안 FCOS3D 방식은 더 적은 카메라로 적용할 수 있고 계산적으로 단순할 수 있는 반면, BEV 구조는 더 많은 계산 비용을 사용하면서 광범위한 다중 시점 문맥(Multi-View Context)과 공간적 일관성을 활용할 수 있다.

카메라 기반 3차원 검출 결과는 여러 카메라 사이에서도 결합할 수 있다. 두 개의 중첩된 시야에서 하나의 객체가 관측되면 각 카메라에서 생성된 상호 보완적인 예측을 공통 로봇 좌표계로 변환할 수 있다. 다중 카메라 연관(Multi-Camera Association)은 시점 차이와 가림을 고려하면서 여러 검출 결과가 동일한 물리적 객체를 나타내는지를 판단해야 한다. 공유 BEV 표현은 이러한 문제를 네트워크의 앞단에서 해결할 수 있으며, 검출 수준 융합(Detection-Level Fusion)은 독립적으로 생성된 3차원 객체 가설을 이후 단계에서 결합할 수 있다.

시간적 추적(Temporal Tracking)은 프레임 단위 3차원 검출 결과의 활용성을 크게 향상시킨다. 검출기는 순간적인 객체 가설을 제공하지만 추적기는 시간에 따라 객체를 연관하고 보다 부드러운 위치, 방향, 속도 상태를 추정한다. 관측되는 객체 움직임에는 객체 자체의 이동과 로봇의 자기 운동(Ego-Motion)이 함께 포함되므로 자기 운동 보상(Ego-Motion Compensation)이 필요하다. 칼만 필터링(Kalman Filtering), 학습 기반 추적(Learned Tracking) 또는 다른 상태 추정(State Estimation) 방법은 측정 잡음을 줄이고 일시적인 검출 실패나 가림 동안에도 객체 상태를 예측할 수 있다.

단안 3차원 검출은 부분 가림(Partial Occlusion)과 영상 절단(Truncation)에 특히 취약하다. 객체가 다른 차량, 기계, 식생, 시설물 뒤에 가려져 일부 영역만 보일 수 있으며 카메라 영상 경계 밖으로 일부가 벗어날 수도 있다. 네트워크는 불완전한 시각 정보로부터 전체 3차원 경계 상자를 추론해야 한다. 학습된 클래스 사전정보는 이를 지원할 수 있지만 높은 신뢰도를 가진 잘못된 기하 구조를 생성할 가능성도 있다. 따라서 시스템은 신뢰도 또는 불확실성 정보를 유지하고 모든 예측 3차원 상자를 동일하게 신뢰할 수 있는 물리적 측정값으로 해석해서는 안 된다.

카메라 기반 3차원 인지는 스테레오 깊이(Stereo Depth), LiDAR, 레이더(Radar), 학습 기반 단안 깊이(Learned Monocular Depth)와 융합하여 강화할 수 있다. 스테레오는 대응 관계(Correspondence)에서 기하학적 깊이를 제공하고, LiDAR는 직접적인 거리 측정값을 제공하며, 레이더는 거리와 상대 속도(Relative Velocity)를 제공한다. 카메라 검출은 세밀한 의미 정보와 객체 분류를 제공한다. 따라서 센서 융합(Sensor Fusion)을 통해 불확실한 카메라 기반 기하 구조를 보정하거나 제약하면서 객체를 식별하고 특성화하는 데 필요한 풍부한 시각 정보를 유지할 수 있다.

이동 로봇(Mobile Robot)에서는 3차원 검출 결과를 지역 객체 지도(Local Object Map)에 배치하여 내비게이션 모듈에 장애물의 위치, 크기, 방향을 제공할 수 있다. 실외 자율이동로봇(Outdoor AMR)은 이러한 관측을 이용하여 보행자, 차량, 장비 및 기타 구조화된 장애물을 판단할 수 있다. 자율주행에서는 검출 결과가 추적과 움직임 예측으로 전달된다. 조작 시스템은 카메라 기반 3차원 객체 가설을 더욱 정밀한 자세 추정(Pose Estimation)의 초기값으로 사용할 수 있지만, 정밀 조작에는 일반적으로 단안 검출만으로 제공되는 것보다 높은 기하학적 정확도가 필요하다.

평가(Evaluation)에서는 2차원 인식 품질과 실제 3차원 정확도를 구분해야 한다. 검출기가 영상 공간에서는 매우 정확한 경계 상자를 생성하면서도 객체를 잘못된 깊이에 배치할 수 있다. 따라서 유용한 평가 지표는 3차원 위치 추정, 중심 거리(Center Distance), 객체 크기, 방향, 서로 다른 거리 범위에서의 검출 품질을 분석해야 한다. 로봇에서는 추가적으로 시간적 안정성(Temporal Stability), 보정 민감도(Calibration Sensitivity), 작은 객체 성능, 불확실성, 도메인 변화(Domain Shift), 계획된 궤적 주변에서 잘못된 깊이 추정이 미치는 영향을 평가해야 한다.

학습 데이터(Training Data)는 모델이 객체 외형, 물리적 크기, 원근법, 거리 사이의 관계를 학습하기 때문에 단안 3차원 객체 검출 성능에 큰 영향을 준다. 카메라 높이, 초점 거리, 영상 해상도, 도로 기하 구조, 객체 분포는 데이터셋과 실제 로봇 사이에서 크게 다를 수 있다. 따라서 자동차 데이터셋에서 실내 AMR, 실외 로봇, 산업 단지, 항만, 창고 등 서로 다른 카메라 구성과 객체 클래스를 가진 환경으로 적용할 때는 미세조정(Fine-Tuning) 또는 재학습(Retraining)이 필요할 수 있다.

실시간 배포(Real-Time Deployment)에서는 영상 해상도, 백본 크기, 특징 피라미드 복잡도, 예측 헤드(Prediction Head), 수치 정밀도(Numerical Precision) 사이의 균형을 고려해야 한다. 높은 해상도는 원거리 객체 인식을 향상시킬 수 있지만 GPU 연산량과 메모리 트래픽(Memory Traffic)을 증가시킨다. 지원되는 엣지 가속기(Edge Accelerator)에서는 FP16 또는 충분히 검증된 INT8 실행으로 추론 비용을 줄일 수 있다. 종단간 지연시간(End-to-End Latency)은 신경망 추론 시간만이 아니라 카메라 획득, 전처리, 추론, 디코딩, 좌표 변환, 추적, ROS2 통신, 경로 계획 모듈로의 전달까지 포함하여 평가해야 한다.

ROS2 아키텍처에서 검출기는 보정된 카메라 영상(Calibrated Camera Image)을 구독하고 클래스, 신뢰도, 위치, 크기, 방향, 좌표 프레임 식별자(Coordinate-Frame Identifier)를 포함하는 타임스탬프 기반 3차원 객체 메시지를 발행할 수 있다. 좌표 변환 인프라(Transform Infrastructure)는 검출 결과를 로봇 또는 지도 좌표계로 변환하며, 추적 노드는 지속적인 객체 상태(Persistent Object State)를 추정할 수 있다. 안정적인 인터페이스를 구성하면 내비게이션, 움직임 예측, 지도 작성, 데이터 기록, 센서 융합 모듈이 FCOS3D의 내부 구현 방식에 직접 의존하지 않고 검출 결과를 사용할 수 있다.

FCOS3D는 투영된 중심(Projected Center), 깊이, 크기, 방향을 다중 스케일 영상 특징에서 예측함으로써 앵커 프리 2차원 객체 검출 원리를 단안 3차원 인지로 확장할 수 있음을 보여준다. 그 중요성은 단순히 3차원 경계 상자를 생성하는 데 있는 것이 아니라 영상 공간 인식(Image-Space Recognition)을 미터 단위 공간 추론(Metric Spatial Reasoning)으로 확장하는 데 있다. 카메라 인지(Camera Perception) 구조에서 이러한 기능은 객체 검출, 깊이 추정, 조감도 표현(BEV Representation)을 객체 추적, 지도 작성, 내비게이션, 이후의 다중 센서 융합(Multi-Sensor Fusion)과 연결한다.

## 02.07. Optical Flow and Scene Flow Estimation [w/Code]

![](images/image7.png){width="7.268055555555556in" height="7.268055555555556in"}

광학 흐름(Optical Flow)은 연속된 카메라 프레임 사이에서 영상 픽셀의 겉보기 움직임(Apparent Motion)을 추정한다. 어떤 객체가 존재하는지만 식별하는 대신, 시각 정보가 시간에 따라 영상 평면(Image Plane)에서 어떻게 이동하는지를 표현한다. 각 픽셀 또는 선택된 영상 위치에는 수평 및 수직 변위를 나타내는 2차원 운동 벡터(Motion Vector)가 할당된다. 로봇에서 이러한 밀집 운동 정보(Dense Motion Information)는 객체 추적, 내비게이션(Navigation), 동적 장애물 분석, 시각 주행거리계(Visual Odometry), 장면 이해(Scene Understanding)를 위한 중요한 시간적 단서(Temporal Cue)를 제공한다.

광학 흐름의 기본 문제는 서로 다른 시간에 획득된 두 영상을 비교하여 첫 번째 프레임에서 관측된 시각적 점이 두 번째 프레임의 어느 위치에 나타나는지를 결정하는 것이다. 위치 (x, y)의 픽셀이 변위 (u, v)만큼 이동하면 추정된 흐름 벡터(Flow Vector)는 이러한 영상 공간 운동(Image-Space Motion)을 나타낸다. 결과적으로 생성되는 흐름장(Flow Field)은 일관된 객체 움직임, 카메라 움직임에 의해 발생하는 운동, 독립적으로 움직이는 객체, 서로 다른 물리적 표면이 서로 다른 겉보기 속도를 가지는 경계를 나타낼 수 있다.

전통적인 광학 흐름 공식화(Classical Optical-Flow Formulation)는 물리적 점의 외형이 인접한 프레임 사이에서 거의 변하지 않는다고 가정하는 밝기 불변성(Brightness Constancy)에 기반하는 경우가 많다. 또한 비교적 작은 움직임과 국부 영역의 공간적 평활성(Spatial Smoothness)을 가정한다. 이러한 가정을 이용하면 영상 미분(Image Derivative)으로 픽셀 운동을 수학적으로 제약할 수 있다. 그러나 조명 변화, 반사, 가림(Occlusion), 모션 블러(Motion Blur), 큰 변위, 비강체 변형(Non-Rigid Deformation)은 이러한 가정을 위반하여 모호하거나 잘못된 흐름 추정을 발생시킬 수 있다.

희소 광학 흐름(Sparse Optical Flow)은 선택된 시각 특징(Visual Feature)을 추적하는 반면, 밀집 광학 흐름(Dense Optical Flow)은 대부분 또는 모든 유효 영상 픽셀의 움직임을 추정한다. 희소 방식은 시각 주행거리계나 특징 추적(Feature Tracking)에서 계산 효율이 높지만 움직이는 전체 표면에 대한 정보는 제한적이다. 밀집 흐름은 객체 분할(Object Segmentation), 동적 장면 추론(Dynamic-Scene Reasoning), 충돌 분석, 움직임 기반 인지(Motion-Aware Perception)를 지원하는 더욱 풍부한 운동 표현을 제공하지만 일반적으로 훨씬 많은 연산량과 메모리를 요구한다.

현대적인 심층 광학 흐름 네트워크(Deep Optical-Flow Network)는 데이터로부터 특징 표현(Feature Representation)과 대응 관계(Correspondence)를 직접 학습한다. 수작업으로 설계된 영상 기울기(Image Gradient)나 국부 정합 규칙(Local Matching Rule)에만 의존하는 대신 연속 프레임에서 특징을 추출하고 이들 사이의 관계를 탐색한다. 학습 기반 모델은 많은 전통적 방법보다 큰 변위, 약한 질감, 복잡한 외형 변화를 처리할 수 있다. 그러나 신뢰성은 여전히 학습 데이터 분포(Training Distribution), 영상 품질, 운동 패턴, 실제 카메라 시스템의 특성에 영향을 받는다.

RAFT는 반복적인 학습 기반 광학 흐름 추정(Iterative Learned Optical-Flow Estimation)의 대표적인 방법이다. 두 입력 영상에서 특징을 추출하고 영상 위치 사이의 유사도를 나타내는 전쌍 상관 표현(All-Pairs Correlation Representation)을 구성한 뒤 순환 갱신 메커니즘(Recurrent Update Mechanism)을 통해 추정된 흐름장을 반복적으로 정제한다. 최종 운동을 한 번의 순전파(Forward Step)로 예측하는 대신 상관 관계 정보와 문맥 정보(Contextual Information)를 이용하여 흐름 추정치를 점진적으로 보정함으로써 다양한 변위 범위에서 정확한 밀집 운동장을 생성한다.

상관 표현(Correlation Representation)은 한 프레임의 픽셀이나 특징이 다음 프레임에서 카메라 또는 객체의 빠른 움직임으로 인해 멀리 이동할 수 있기 때문에 중요하다. 여러 가능한 대응 관계에 대한 정보를 유지함으로써 네트워크는 작은 국부 탐색 영역(Local Search Window)을 넘어서는 움직임을 추론할 수 있다. 순환 갱신 연산자(Recurrent Update Operator)는 현재 흐름 가설(Flow Hypothesis) 주변의 상관 정보를 반복적으로 참조하여 점진적인 보정값을 예측한다. 이를 통해 반복적인 정제 과정에서 대응 관계가 점차 정밀해질 수 있다.

광학 흐름 자체는 물리적인 3차원 운동(Physical 3D Motion)을 직접 나타내지 않는다. 2차원 영상 변위는 객체 운동, 카메라 운동, 객체 깊이, 초점 거리(Focal Length), 관측 기하(Viewing Geometry)에 동시에 영향을 받는다. 따라서 동일한 물리적 속도라도 카메라와의 거리에 따라 서로 다른 영상 흐름이 발생할 수 있다. 또한 로봇이 움직이면 정적인 환경에서도 상당한 광학 흐름이 생성된다. 따라서 로봇에서 흐름을 해석하려면 카메라 보정(Camera Calibration), 자기 운동(Ego-Motion) 정보, 깊이 또는 추가적인 기하학적 제약이 필요하다.

자기 운동은 영상 전체에 특징적인 흐름 패턴을 생성한다. 전진 운동 중에는 정적인 장면의 점들이 이동 방향과 관련된 영역을 중심으로 바깥쪽으로 확산되는 것처럼 보일 수 있으며, 회전 운동은 병진 이동이 없더라도 영상 전체에 광범위한 움직임을 발생시킬 수 있다. 시각 주행거리계와 관성 측정(Inertial Measurement)을 이용하여 카메라 운동을 추정하고 정적 환경에서 예상되는 흐름을 예측할 수 있다. 이러한 예상 운동과 실제 흐름의 차이는 독립적으로 움직이는 객체나 잘못된 기하학적 가정의 증거가 될 수 있다.

동적 객체 추론(Dynamic-Object Reasoning)은 광학 흐름의 가장 유용한 로봇 응용 중 하나이다. 객체 검출(Object Detection)과 분할(Segmentation)은 의미적 객체를 식별하고, 흐름은 해당 객체의 가시 표면이 프레임 사이에서 어떻게 움직이는지를 나타낸다. 이러한 표현을 결합하면 로봇은 정적 객체와 이동 객체를 구분하고 영상 공간 운동을 추정하며 객체 추적 상태를 유지하고 예상하지 못한 움직임을 식별할 수 있다. 이는 보행자, 차량, 이동 장비, 문, 매니퓰레이터(Manipulator) 등 내비게이션 결정에 영향을 주는 동적 요소 주변에서 특히 유용하다.

가림은 한 프레임에서 보이던 일부 픽셀이 다음 프레임에서 사라지고 이전에 가려졌던 영역이 새롭게 나타나기 때문에 근본적인 문제를 만든다. 따라서 모든 픽셀에 항상 유효한 대응 관계가 존재하는 것은 아니다. 흐름 알고리즘은 임의의 대응점을 강제로 할당하기보다 이러한 영역을 식별하거나 신뢰도를 낮추는 것이 바람직하다. 순방향-역방향 일관성 검사(Forward-Backward Consistency Check), 학습 기반 가림 추론(Learned Occlusion Reasoning), 신뢰도 추정(Confidence Estimation), 시간 모델(Temporal Model)을 이용하여 신뢰성이 낮은 흐름을 식별할 수 있지만 가림 경계는 여전히 밀집 운동 추정에서 가장 어려운 영역에 속한다.

장면 흐름(Scene Flow)은 광학 흐름의 개념을 2차원 영상 운동에서 장면 점(Scene Point)의 3차원 운동으로 확장한다. 영상 평면의 변위만을 추정하는 대신 장면 흐름은 가시적인 3차원 점이 시간에 따라 공간에서 어떻게 움직이는지를 추정한다. 따라서 장면 흐름 벡터(Scene-Flow Vector)는 물리적 운동을 3차원으로 표현하며 동적 기하 구조(Dynamic Geometry)를 보다 직접적으로 나타낼 수 있다. 이는 미래의 객체 위치와 충돌 위험을 추론해야 하는 자율 시스템에서 특히 유용하다.

장면 흐름을 추정하려면 일반적인 단안 광학 흐름보다 추가적인 기하 정보가 필요하다. 스테레오 카메라(Stereo Camera)는 연속된 시간 단계에서 깊이를 제공하여 2차원 대응점을 3차원 좌표로 변환할 수 있다. RGB-D 카메라나 LiDAR는 직접적인 깊이 측정값을 제공하며, 단안 시스템은 학습 기반 깊이(Learned Depth)를 광학 흐름 및 카메라 운동과 결합할 수 있다. 최종 장면 흐름의 품질은 운동 대응 관계와 깊이 추정의 정확도 및 시간적 일관성(Temporal Consistency)에 크게 의존한다.

스테레오 시스템에서는 시각과 보정 정보를 이용하여 시간 t에서 장면 점을 3차원으로 재구성하고, 시간 t+1에서 해당 관측과 정합한 뒤 다시 3차원 위치를 재구성할 수 있다. 두 3차원 위치의 차이를 계산하면 장면 흐름을 추정할 수 있다. 실제로는 대응 관계, 시차(Disparity), 보정, 동기화(Synchronization), 가림 오차가 모두 최종 운동 불확실성(Motion Uncertainty)에 영향을 준다. 광학 흐름, 시차, 장면 운동을 공동으로 추정하는 모델은 서로 관련된 기하학적 제약을 이용하여 각 추정 결과를 상호 강화할 수 있다.

장면 흐름은 객체 운동(Object Motion)과 자기 운동을 구분해야 한다. 이동하는 카메라에서 정적인 벽은 카메라에 대해 움직이는 것처럼 보이지만 월드 좌표계(World Coordinate System)에서는 위치가 고정되어 있다. 시각-관성 주행거리계(Visual-Inertial Odometry), 휠 오도메트리(Wheel Odometry), GNSS/IMU 또는 동시적 위치추정 및 지도작성(SLAM)을 이용하여 관측값을 공통 좌표계로 변환하면 로봇의 움직임을 보상할 수 있다. 이후 남는 3차원 변위는 독립적인 장면 동역학(Scene Dynamics)을 보다 직접적으로 나타내며 이동 장애물의 추적과 예측에 활용할 수 있다.

시간적 일관성은 프레임 간 흐름의 잡음이 불안정한 운동 추정을 발생시킬 수 있기 때문에 중요하다. 작은 대응 관계 오차도 특히 장거리 또는 객체 경계 부근에서 속도 벡터를 크게 변동시킬 수 있다. 객체 추적, 시간 필터링(Temporal Filtering), 순환 신경망(Recurrent Network), 다중 프레임 특징 집계(Multi-Frame Feature Aggregation)를 이용하면 긴 시퀀스에 걸쳐 추정값을 안정화할 수 있다. 그러나 과도한 평활화는 실제 가속이나 급격한 방향 변화를 억제할 수 있으므로 로봇 안전과 계획에 중요한 동적 특성을 유지해야 한다.

광학 흐름과 장면 흐름은 객체 검출 및 분할과 자연스럽게 상호 보완된다. 검출기는 객체 클래스와 대략적인 위치를 제공하고, 분할은 객체 픽셀을 식별하며, 광학 흐름은 객체의 영상 운동을 설명하고, 깊이는 이러한 관측을 3차원 기하 구조로 확장한다. 이러한 출력을 결합하면 객체 중심 속도 추정(Object-Centric Velocity Estimation), 운동 분할(Motion Segmentation), 동적 점유 추론(Dynamic Occupancy Reasoning), 궤적 예측(Trajectory Prediction)이 가능하다. 이러한 통합은 외형, 기하 구조, 운동을 서로 독립적인 인지 작업으로 처리하는 것보다 더 풍부한 정보를 제공한다.

조감도 인지(BEV Perception) 역시 흐름으로부터 얻은 시간적 운동 정보를 통합할 수 있다. 영상 공간 운동은 특징을 조감도 표현(Bird's-Eye-View Representation)으로 투영하거나 집계하기 전에 프레임 사이에서 특징을 정렬하는 데 도움을 줄 수 있다. 장면 흐름 추정은 미터 단위 공간 좌표계에서 움직임을 직접 표현할 수 있으며 동적 점유 격자(Dynamic Occupancy Grid) 또는 미래 점유 예측(Future Occupancy Prediction)에 활용될 수 있다. 이를 통해 카메라의 시간적 인지와 경로 계획 시스템에서 사용하는 운동 인식 공간 표현(Motion-Aware Spatial Representation)을 연결할 수 있다.

내비게이션에서 흐름은 충돌까지의 시간(Time-to-Collision) 단서, 이동 장애물에 대한 증거, 주행 가능한 장면의 동적 특성 정보를 제공할 수 있다. 영상 영역이 빠르게 확장되는 현상은 접근하는 표면을 나타낼 수 있으며, 독립적으로 움직이는 흐름 패턴은 횡단하는 보행자나 차량을 나타낼 수 있다. 그러나 영상 속도만으로 물리적 거리를 결정할 수 없기 때문에 이러한 단서는 깊이 및 기하학적 추론과 결합해야 한다. 서로 다른 조건에서는 빠르게 움직이는 먼 객체와 느리게 움직이는 가까운 객체가 유사한 영상 평면 운동을 나타낼 수 있다.

조작(Manipulation)에서 광학 흐름은 움직이는 객체, 변형 가능한 물체(Deformable Material), 사람의 손, 도구 또는 로봇 상호작용에 의해 발생하는 운동을 추적하는 데 활용할 수 있다. 장면 흐름은 동적 파지(Dynamic Grasping), 시각 서보잉(Visual Servoing), 움직이는 대상의 조작을 위해 더욱 풍부한 3차원 변위 정보를 제공할 수 있다. 정밀 조작은 높은 공간 및 시간 정확도를 요구하므로 흐름을 단독 운동 측정 수단으로 사용하기보다 깊이 카메라, 스테레오 비전, 자세 추정(Pose Estimation), 촉각 센싱(Tactile Sensing), 힘 피드백(Force Feedback)과 통합할 수 있다.

광학 흐름의 평가는 일반적으로 예측 운동 벡터와 기준 운동 벡터 사이의 종점 오차(Endpoint Error)와 특정 오차 임계값을 초과하는 픽셀 비율을 측정한다. 장면 흐름 평가는 추가적으로 3차원 변위와 깊이 관련 오차를 고려한다. 로봇 시스템에서는 시간적 안정성, 경계 정확도, 가림 처리, 지연시간(Latency), 큰 움직임 성능, 작은 객체의 운동, 자기 운동 보상, 조명 변화, 블러, 반사, 날씨, 도메인 변화(Domain Shift)에서의 실패도 평가해야 한다.

실시간 배포(Real-Time Deployment)에서는 영상 해상도, 특징 추출 비용, 상관 관계 계산(Correlation Computation), 정제 반복 횟수, 메모리 소비량 사이의 균형이 필요하다. 밀집 학습 기반 흐름은 넓은 공간 범위에서 대응 관계를 평가해야 하므로 계산량이 클 수 있다. 저해상도 처리, 최적화된 상관 구조, FP16 실행, 정제 반복 횟수 감소, 전용 가속기(Specialized Accelerator)를 통해 지연시간을 줄일 수 있다. 그러나 계산량을 줄일 때 작은 이동 객체와 얇은 경계 정보가 먼저 손실되는 경우가 많으므로 이러한 최적화는 신중하게 검증해야 한다.

카메라 동기화(Camera Synchronization)와 타임스탬프(Timestamp)는 정량적인 운동 추정에서 매우 중요하다. 광학 흐름은 프레임 사이의 시간 간격에 본질적으로 의존하며 장면 속도(Scene Velocity)는 변위를 정확하게 알려진 시간 간격으로 나누어 계산해야 한다. 프레임 간격의 변화, 버퍼링(Buffering), 프레임 손실(Dropped Frame), 동기화되지 않은 스테레오 카메라는 체계적인 속도 오차를 발생시킬 수 있다. 따라서 로봇 파이프라인은 하위 소프트웨어에서 처리된 메시지를 수신한 시간이 아니라 원래의 영상 획득 타임스탬프(Acquisition Timestamp)를 유지해야 한다.

ROS2 아키텍처에서 광학 흐름 또는 장면 흐름 노드는 동기화된 카메라 영상, 보정 데이터(Calibration Data), 깊이 정보, 자기 운동 추정값을 구독할 수 있다. 출력에는 밀집 2차원 흐름장(Dense 2D Flow Field), 신뢰도 지도(Confidence Map), 3차원 운동 벡터, 동적 마스크(Dynamic Mask), 객체 수준 속도(Object-Level Velocity) 등이 포함될 수 있다. 객체 추적, 지도 작성(Mapping), 내비게이션, 움직임 예측, 센서 융합(Sensor Fusion) 모듈은 대응 관계 추정에 사용된 내부 알고리즘에 직접 의존하지 않고 이러한 표준화된 운동 표현을 사용할 수 있다.

광학 흐름과 장면 흐름은 궁극적으로 카메라 인지(Camera Perception)에 명시적인 시간적 운동(Temporal Motion) 정보를 추가한다. 광학 흐름은 시각적 관측이 영상 평면에서 어떻게 이동하는지를 설명하며, 장면 흐름은 대응 관계와 기하 구조를 결합하여 이러한 추론을 3차원 공간으로 확장한다. 객체 검출, 분할, 단안 또는 스테레오 깊이, 조감도 인지, 3차원 객체 검출과 함께 이러한 기술은 로봇이 객체가 무엇이고 어디에 있는지를 이해하는 수준에서 주변의 물리적 세계가 시간에 따라 어떻게 변화하고 있는지를 추정하는 수준으로 발전할 수 있도록 한다.

## 02.08. Object Pose Estimation 6DoF FoundPose [w/Code]

![](images/image8.png){width="7.268055555555556in" height="7.268055555555556in"}

객체 자세 추정(Object Pose Estimation)은 물리적 객체가 카메라, 로봇 또는 월드 좌표계(World Coordinate Frame)에 대해 어떤 위치와 방향을 가지는지를 결정한다. 완전한 6자유도(Six-Degree-of-Freedom, 6DoF) 표현에서 자세(Pose)는 3개의 병진 성분(Translational Component)과 3개의 회전 성분(Rotational Component)으로 구성된다. 이를 통해 객체 인지는 단순히 객체가 무엇이며 영상의 어디에 나타나는지를 식별하는 수준에서 기하학적 상호작용, 조작(Manipulation), 검사(Inspection), 추적(Tracking), 공간 추론(Spatial Reasoning)에 필요한 강체 변환(Rigid Transformation)을 추정하는 수준으로 확장된다.

6DoF 자세는 일반적으로 SE(3)에 속하는 강체 변환(Rigid-Body Transformation)으로 표현된다. 병진(Translation)은 세 개의 공간 축을 따라 객체의 위치를 나타내며, 회전(Rotation)은 객체의 방향을 나타낸다. 시스템 인터페이스에 따라 회전은 회전 행렬(Rotation Matrix), 쿼터니언(Quaternion), 축-각 벡터(Axis-Angle Vector), 오일러 각(Euler Angle) 등으로 표현할 수 있다. 회전 R과 병진 t를 결합하면 객체 좌표계(Object Coordinate Frame)에 정의된 점을 카메라 또는 다른 기준 좌표계로 변환할 수 있다.

자세 추정에서는 객체 좌표계(Object Coordinate System)를 명확하게 정의해야 한다. CAD 모델, 재구성된 메시(Reconstructed Mesh) 또는 기준 객체 표현(Reference Object Representation)은 일반적으로 객체에 고정된 원점과 서로 직교하는 세 개의 축을 정의한다. 추정된 자세는 관측된 장면에서 이러한 좌표계가 어디에 위치하고 어떤 방향을 가지는지를 나타낸다. 좌표 정의의 일관성은 매우 중요하며, 잘못 정의된 객체 좌표계를 기준으로 수치적으로 정확한 변환을 계산하더라도 파지, 검사 또는 로봇 운동에서 잘못된 결과를 생성할 수 있다.

전통적인 객체 자세 추정 파이프라인은 객체 검출(Object Detection) 또는 분할(Segmentation)로 시작한 뒤 국부 시각 특징(Local Visual Feature)을 추출하고 알려진 객체 모델과 연결된 특징과 정합(Matching)하는 방식으로 구성되는 경우가 많다. 대응되는 2차원 영상 점과 3차원 모델 점은 기하학적 자세 추정을 위한 제약을 제공한다. 관점-n-점(Perspective-n-Point, PnP) 방법은 이러한 대응 관계로부터 카메라와 객체 사이의 변환을 추정하며, RANSAC은 잘못된 정합을 제거하고 이후 비선형 정제(Nonlinear Refinement)를 통해 최종 자세를 개선할 수 있다.

객체의 질감이 약하거나 반복 패턴을 포함하고, 반사 표면을 가지거나, 시점 변화가 크거나, 부분적인 가림(Partial Occlusion)이 발생하면 특징 정합(Feature Matching)이 어려워진다. 산업용 객체에서는 가공 부품이 넓고 균일한 표면이나 반복적인 기하 구조를 포함하는 경우가 많기 때문에 이러한 문제가 더욱 두드러질 수 있다. 따라서 자세 추정 시스템은 외형 변화에도 구별 능력을 유지하면서 관측 영상 영역과 객체 모델 사이에 신뢰할 수 있는 대응 관계를 설정할 수 있을 정도의 기하학적 정보를 보존하는 표현이 필요하다.

FoundPose는 제한적으로 라벨링된 자세 데이터셋에서 특정 작업에 맞춰 학습한 특징에만 의존하는 대신 강력한 사전학습 시각 특징(Pretrained Visual Feature)을 활용하는 현대적인 객체 자세 추정 방향을 나타낸다. 파운데이션 모델 표현(Foundation-Model Representation)은 다양한 객체와 장면에 걸쳐 전이 가능한 시각 구조를 인코딩할 수 있다. 이는 모든 새로운 부품에 대해 광범위한 자세 전용 재학습을 수행하지 않고도 이전에 보지 못했거나 학습 데이터에서 충분히 표현되지 않은 객체를 처리해야 하는 로봇 시스템에서 특히 중요하다.

이러한 파운데이션 특징 기반 접근법의 핵심 목표 중 하나는 일반화 가능한 자세 추정(Generalizable Pose Estimation)이다. 기존의 객체 특화 네트워크(Object-Specific Network)는 학습 객체와 실제 배포 객체가 밀접하게 일치할 경우 높은 성능을 제공할 수 있지만 새로운 산업용 부품을 추가할 때 영상 수집, 어노테이션 생성, 모델 재학습이 필요할 수 있다. 보다 일반적인 접근법은 재사용 가능한 시각 표현과 객체 기준 모델을 함께 이용하여 객체별 학습을 크게 줄이면서 새로운 객체로 자세 추정을 확장하는 것을 목표로 한다.

기준 정보(Reference Information)는 CAD 기하 구조, 렌더링된 객체 시점(Rendered Object View), 기준 영상 또는 이들의 조합으로부터 생성할 수 있다. 정확한 CAD 모델을 사용할 수 있는 경우 합성 렌더링(Synthetic Rendering)은 객체를 알려진 다양한 시점에서 가상으로 관측할 수 있기 때문에 특히 유용하다. 이러한 기준 시점에서 추출된 특징은 시각적 외형과 알려진 객체 기하 구조를 연결하는 데이터베이스를 구성할 수 있다. 추론 단계에서는 관측 영상의 특징을 이러한 기준 표현과 비교하여 대응 관계를 복원할 수 있다.

추론 파이프라인(Inference Pipeline)은 일반적으로 대상 객체를 찾거나 관련 영상 영역으로 처리를 제한하기 위한 분할 마스크(Segmentation Mask)를 얻는 과정에서 시작한다. 이후 관측된 객체에서 시각 특징을 추출하고 기준 시점 또는 객체 표현에서 생성된 특징과 비교한다. 후보 대응 관계(Candidate Correspondence)는 관측 영상 위치가 알려진 객체 기하 구조와 어떻게 연결되는지에 대한 가설을 제공한다. 기하학적 추정(Geometric Estimation)은 이러한 대응 관계를 하나 이상의 후보 6DoF 자세로 변환한다.

모든 시각적 정합이 기하학적으로 유효한 것은 아니기 때문에 강인한 대응 관계 필터링(Robust Correspondence Filtering)이 필수적이다. 배경 픽셀, 객체를 가리는 다른 물체, 반복적인 질감, 시각적으로 유사한 객체 영역은 잘못된 대응 관계를 생성할 수 있다. 기하학적 일관성 검사(Geometric Consistency Test)와 강인한 추정기(Robust Estimator)를 이용하면 이러한 이상치(Outlier)를 상당 부분 제거할 수 있다. 이후 대응 품질, 재투영 일관성(Reprojection Consistency), 특징 유사도 또는 기타 신뢰도 척도에 따라 자세 가설을 평가하고 가장 강력한 후보를 정제 단계로 전달할 수 있다.

자세 정제(Pose Refinement)는 예측된 객체 구성을 관측 결과와 더욱 정확하게 정렬하여 초기 자세 추정값을 개선한다. 사용 가능한 센서와 표현 방식에 따라 영상 정보, 객체 마스크, 렌더링 영상, 깊이 측정값, 포인트 클라우드(Point Cloud), 기하학적 거리 등을 정제에 활용할 수 있다. 반복적인 과정에서 회전과 병진을 조정하여 예측된 객체 외형 또는 기하 구조와 실제 관측 사이의 차이를 줄인다. 특징 정합으로 올바른 대략적 자세 영역을 찾았지만 조작에 필요한 정밀도가 부족한 경우 자세 정제가 특히 중요하다.

깊이 정보(Depth Information)는 자세 모호성(Pose Ambiguity)을 크게 줄일 수 있다. RGB-D 카메라를 사용하면 영상 대응점을 실제로 측정된 3차원 점과 연결하여 직접적인 기하학적 제약을 제공할 수 있다. 스테레오 비전(Stereo Vision)은 삼각측량(Triangulation)을 통해 깊이를 제공하며, LiDAR는 큰 객체에 대해 추가적인 표면 측정값을 제공할 수 있다. RGB 전용 추정은 카메라가 저렴하면서 풍부한 정보를 제공한다는 장점이 있지만, 깊이 기반 정제는 병진 정확도를 향상시키고 시각적으로는 타당하지만 기하학적으로 잘못된 자세 가설을 제거하는 데 도움을 줄 수 있다.

객체 대칭성(Object Symmetry)은 자세 평가에서 가장 중요한 개념적 문제 중 하나이다. 원통형 부품, 기어, 병 또는 회전 대칭 부품은 서로 다른 여러 방향에서도 구별하기 어려운 동일한 관측 결과를 생성할 수 있다. 따라서 하나의 수치적 회전값만을 정답으로 간주하면 물리적으로 동등한 자세를 잘못된 것으로 평가할 수 있다. 자세 추정 시스템과 평가 지표는 알려진 객체 대칭성을 명시적으로 고려하여 임의의 좌표 규칙이 아니라 실제 관측 가능한 기하 구조와 작업 관련성을 기준으로 예측 결과를 평가해야 한다.

부분 가림(Partial Occlusion)은 또 다른 중요한 문제이다. 객체가 빈(Bin) 내부에 있거나 다른 부품 뒤에 위치하거나, 사람이 들고 있거나, 작업 도구에 일부 가려져 로봇이 객체의 일부분만 관측할 수 있다. 보이는 영역에 특징 기반 정합을 위한 충분한 구별 정보가 남아 있을 수 있지만 유효한 대응점의 수와 공간적 분포는 감소한다. 따라서 강인한 자세 추정은 신뢰도(Confidence)를 표현해야 하며, 심하게 가려진 객체의 자세를 완전히 노출된 객체와 동일한 확실성을 가진 것으로 해석해서는 안 된다.

자세 추정은 영상 해상도(Image Resolution)와 관측 거리(Viewing Distance)에도 민감하다. 작은 객체나 멀리 있는 부품은 영상에서 차지하는 픽셀 수가 적기 때문에 특징 정합과 방향 추정에 사용할 수 있는 정보가 감소한다. 모션 블러(Motion Blur)와 초점 흐림(Defocus)은 세밀한 기하 정보를 더욱 약화시킨다. 높은 해상도의 영상은 대응 관계 품질을 향상시킬 수 있지만 특징 추출 비용과 메모리 사용량을 증가시킨다. 따라서 카메라 배치는 예상 객체 크기, 작업 거리, 요구 자세 정확도, 사용 가능한 연산 자원을 함께 고려해야 한다.

카메라 보정(Camera Calibration)은 미터 단위 자세 추정(Metric Pose Estimation)의 기하학적 기반을 제공한다. 내부 파라미터(Intrinsic Parameter)는 카메라 광선과 영상 픽셀 사이의 매핑을 정의하고, 왜곡 파라미터(Distortion Parameter)는 렌즈에 의해 발생하는 편차를 설명한다. 자세 결과를 로봇에서 사용하려면 외부 보정(Extrinsic Calibration)을 통해 카메라와 로봇 좌표계 사이의 변환을 결정해야 한다. 손-눈 보정(Hand-Eye Calibration)은 특히 매니퓰레이터에 장착된 카메라나 로봇 작업 공간을 외부에서 관측하는 고정 카메라에서 중요하다.

손목 장착 카메라(Eye-in-Hand) 구성에서는 카메라가 매니퓰레이터와 함께 이동하므로 추정된 카메라-객체 자세(Camera-to-Object Pose)를 로봇의 운동학적 변환(Kinematic Transformation)과 결합하여 로봇 베이스 좌표계에서 객체 위치를 계산해야 한다. 외부 고정 카메라(Eye-to-Hand) 구성에서는 카메라가 외부에 고정되어 있으므로 카메라와 로봇 베이스 사이의 관계를 보정해야 한다. 두 경우 모두 변환 오차는 비전 알고리즘이 카메라 기준 자세를 정확하게 추정하더라도 파지와 조작 정확도에 직접적으로 전파된다.

객체 검출(Object Detection), 분할(Segmentation), 자세 추정(Pose Estimation)은 자연스러운 인지 계층(Perception Hierarchy)을 형성한다. 객체 검출은 후보 객체를 식별하고, 분할은 객체의 가시 영역을 분리하며, 6DoF 자세 추정은 객체의 공간 변환을 결정한다. 인스턴스 마스크(Instance Mask)는 배경 특징을 억제하여 대응 관계 품질을 향상시킬 수 있으며, 객체 클래스 정보는 적절한 CAD 또는 기준 표현을 선택하는 데 사용할 수 있다. 이러한 모듈식 구조를 이용하면 서로 다른 검출기나 분할기를 동일한 자세 추정 하위 시스템과 결합할 수 있다.

자세 추정은 카메라 기반 3차원 객체 검출(Camera-Based 3D Object Detection)과 자연스럽게 연결되지만 서로 다른 수준의 기하학적 세부 정보를 제공한다. 3차원 검출기는 일반적으로 내비게이션과 추적에 적합한 대략적인 방향성 경계 상자(Oriented Bounding Box)를 예측하지만, 6DoF 자세 추정은 객체별 좌표계(Object-Specific Coordinate Frame)를 실제 물리 객체에 정렬하는 것을 목표로 한다. 조작과 검사에서는 특정 장착 구멍, 손잡이, 커넥터 또는 파지 표면의 정확한 방향을 알아야 하므로 객체가 특정 3차원 경계 상자 안에 존재한다는 정보만으로는 충분하지 않다.

로봇 조작(Robotic Manipulation)에서는 추정된 객체 자세를 사전에 정의되거나 생성된 파지 자세(Grasp Pose)와 결합할 수 있다. 객체 좌표계를 기준으로 정의된 파지 자세는 추정된 객체 변환을 이용하여 로봇 좌표계로 변환할 수 있다. 이후 운동 계획(Motion Planning)은 목표 구성까지 충돌이 없는 궤적을 생성한다. 병진 또는 회전 오차는 말단장치(End-Effector)의 목표 자세에 직접 전달되므로 커넥터 삽입이나 정밀 조립과 같은 작업은 단순한 픽앤플레이스(Pick-and-Place)보다 훨씬 높은 자세 정확도를 요구한다.

산업 검사(Industrial Inspection)는 또 다른 중요한 응용 분야이다. 알려진 CAD 모델과 추정된 자세를 이용하면 특정 표면, 구멍, 용접부, 라벨 또는 검사 영역이 어디에 나타나야 하는지를 로봇이 예측할 수 있다. 이후 매니퓰레이터 또는 이동형 검사 시스템은 이러한 특징을 기준으로 카메라나 다른 센서를 배치할 수 있다. 따라서 자세 추정은 단순한 객체 인식 기능뿐만 아니라 디지털 제품 정의(Digital Product Definition)와 실제 물리적 검사 동작을 연결하는 기하학적 연결고리 역할을 한다.

시간적 추적(Temporal Tracking)은 객체나 카메라가 움직이는 상황에서 자세 추정값을 안정화할 수 있다. 각 프레임을 독립적으로 추정하는 대신 이전 자세를 다음 관측을 위한 유용한 사전정보(Prior)로 사용할 수 있다. 필터링 또는 최적화(Optimization)는 자세의 흔들림(Jitter)을 줄이면서 비정상적인 급격한 변화를 탐지할 수 있다. 이동 객체의 경우 자세 추적을 통해 병진 속도와 회전 속도까지 추정할 수 있다. 그러나 객체가 빠르게 이동하거나 회전하고, 놓이거나, 일시적으로 추적에서 사라지는 경우에는 시간적 사전정보가 새로운 관측보다 지나치게 큰 영향을 주지 않도록 해야 한다.

6DoF 자세 추정의 평가(Evaluation)는 객체의 기하 구조와 대칭성을 고려하면서 병진과 회전을 모두 측정해야 한다. 재투영 오차(Reprojection Error)는 변환된 모델 점이 영상에서 얼마나 정확하게 정렬되는지를 평가하며, 3차원 모델 거리 지표(3D Model-Distance Metric)는 예측 자세와 기준 자세 사이의 기하학적 일치도를 평가한다. 응용 중심 평가에서는 파지 성공률, 삽입 정확도, 검사 정렬 정확도, 가림에 대한 강인성, 미관측 객체 일반화(Unseen-Object Generalization), 추론 지연시간(Inference Latency), 실패 검출 품질도 함께 측정해야 한다.

일반화(Generalization)는 FoundPose 방식의 접근에서 특히 중요하다. 로봇 환경에는 자세 학습 데이터셋에 포함되지 않았던 객체가 빈번하게 존재하기 때문이다. 재사용 가능한 파운데이션 특징(Foundation Feature)의 실용적 가치는 광범위한 객체별 어노테이션과 재학습에 대한 의존성을 줄이는 데 있다. 그러나 특이한 재질, 투명 표면, 심한 반사, 극단적인 크기 차이, 시각적으로 반복적인 산업 부품에서는 여전히 모호한 대응 관계가 발생할 수 있으므로 실제 운용 영역(Operational Domain)에서 일반화 성능을 검증해야 한다.

실시간 배포(Real-Time Deployment)에서는 특징 품질, 영상 해상도, 기준 시점 수, 대응 관계 탐색, 기하학적 자세 가설 생성, 자세 정제 사이의 균형을 고려해야 한다. 대형 파운데이션 모델 특징은 정합 품질을 향상시킬 수 있지만 GPU 메모리와 지연시간을 증가시킨다. 알려진 CAD 모델의 기준 특징은 사전에 계산(Precomputation)하여 온라인 연산량을 줄일 수 있다. 근사 최근접 이웃 탐색(Approximate Nearest-Neighbor Search), 저정밀 연산(Reduced Precision), 최적화된 특징 추출, 높은 신뢰도의 자세 가설만 선택적으로 정제하는 방법을 통해 실행 성능을 더욱 향상시킬 수 있다.

ROS2 아키텍처에서 자세 추정 노드(Pose-Estimation Node)는 보정된 카메라 영상, 객체 검출 또는 분할 마스크, 사용 가능한 경우 깊이 정보, 객체 모델 식별자(Object-Model Identifier)를 구독할 수 있다. 출력에는 객체 식별 정보, 신뢰도, 병진, 회전, 타임스탬프(Timestamp), 기준 좌표계 정보가 포함될 수 있다. TF 변환을 이용하면 자세를 카메라, 로봇 베이스, 말단장치 또는 지도 좌표계로 표현할 수 있으며, 이를 통해 조작, 검사, 추적, 경로 계획, 기록 모듈이 일관된 공간 인터페이스를 사용할 수 있다.

객체 자세 추정은 궁극적으로 시각적 객체 인식(Visual Object Recognition)을 실제 행동에 사용할 수 있는 강체 변환(Actionable Rigid-Body Transformation)으로 변환한다. 6자유도 추론(6DoF Reasoning)은 객체가 어디에 존재하는지만이 아니라 객체의 전체 좌표계가 물리적 공간에서 어떤 방향으로 배치되어 있는지를 정의한다. FoundPose 방식의 전이 가능한 사전학습 시각 특징(Transferable Pretrained Visual Feature)은 이러한 능력을 더욱 일반화된 객체 처리로 확장하며, 객체 검출과 분할을 기하학적 대응 관계, 자세 정제, 로봇 조작, 산업 검사, 더 넓은 피지컬 AI(Physical AI) 상호작용과 연결한다.

## 02.09. Camera Perception ROS2 Node Integration [w/Code]

![](images/image9.png){width="7.268055555555556in" height="7.268055555555556in"}

로봇 시스템에서 카메라 인지(Camera Perception)는 개별 비전 알고리즘이 신뢰할 수 있는 소프트웨어 파이프라인(Software Pipeline)으로 통합될 때 비로소 실제 운용에 유용해진다. ROS2는 카메라 드라이버, 전처리 구성요소, 신경망 추론 모듈, 기하학적 처리, 객체 추적(Tracking), 센서 융합(Sensor Fusion), 내비게이션(Navigation)이 독립적인 노드(Node)로 동작할 수 있는 분산 통신 프레임워크(Distributed Communication Framework)를 제공한다. 이러한 모듈식 구성은 카메라 하드웨어나 하위 로봇 제어 소프트웨어와 강하게 결합하지 않으면서 인지 알고리즘을 지속적으로 발전시킬 수 있도록 한다.

일반적인 카메라 인지 아키텍처는 물리적 센서로부터 영상을 획득하는 하나 이상의 카메라 드라이버 노드(Camera Driver Node)에서 시작한다. 드라이버는 카메라 보정 정보(Camera Calibration Information)와 영상 획득 타임스탬프(Acquisition Timestamp)를 함께 영상 스트림으로 발행한다. 센서 구성에 따라 토픽(Topic)은 RGB 영상, 스테레오 영상 쌍(Stereo Pair), 깊이 영상(Depth Image), 동기화된 다중 카메라 스트림을 포함할 수 있다. 인지 결과는 이후의 처리가 완료된 시간이 아니라 실제 물리적 관측이 발생한 시점을 나타내야 하므로 센서 원본 타임스탬프를 유지하는 것이 필수적이다.

ROS2는 일반적으로 표준화된 영상 메시지(Image Message)를 통해 카메라 영상을 표현하고, 카메라 보정 파라미터는 카메라 정보 메시지(Camera Information Message)를 통해 전달한다. 보정 데이터에는 하위 기하 처리에 필요한 내부 파라미터(Intrinsic Parameter), 왜곡 정보(Distortion Information), 영상 크기, 투영 관련 파라미터가 포함된다. 영상 데이터와 보정 메타데이터(Calibration Metadata)를 분리하면 서로 다른 카메라 모델에서도 공통 인터페이스를 유지할 수 있다. 각 노드는 보정 정보가 실제 카메라 해상도 및 운용 모드와 일치하는지 확인해야 한다.

영상 전처리(Image Preprocessing)는 전용 노드로 구현하거나 추론 구성요소에 통합할 수 있다. 일반적인 처리에는 디베이어링(Debayering), 왜곡 보정(Distortion Correction), 정류(Rectification), 크기 조정(Resizing), 정규화(Normalization), 색 공간 변환(Color-Space Conversion), 관심 영역 추출(Region-of-Interest Extraction)이 포함된다. 스테레오 파이프라인에서는 좌우 영상을 기하학적으로 일관되게 정류해야 한다. 다중 카메라 시스템은 독립적으로 획득된 영상 특징을 이후 공통 로봇 좌표계로 변환할 수 있도록 각 카메라의 식별 정보와 보정 정보를 유지해야 한다.

ROS2 노드 그래프(Node Graph)는 가능한 경우 하드웨어 의존 기능과 인지 알고리즘을 분리해야 한다. 카메라 드라이버는 주로 센서 설정과 데이터 획득을 관리하고, 객체 검출 노드(Object-Detection Node)는 카메라를 직접 제어하는 대신 표준화된 영상 메시지를 입력받도록 구성하는 것이 바람직하다. 이러한 분리는 하드웨어 인터페이스를 다시 작성하지 않고도 YOLO, RT-DETR, 분할(Segmentation), 깊이 추정, 광학 흐름(Optical Flow), 자세 추정(Pose Estimation) 구성요소를 교체할 수 있게 하며, 이전에 기록된 센서 데이터를 이용한 오프라인 재생(Offline Replay)도 단순화한다.

카메라 인지 노드는 서로 다른 수준의 정보를 제공할 수 있다. 2차원 검출기는 클래스, 신뢰도, 영상 공간 경계 상자(Image-Space Bounding Box)를 발행할 수 있으며, 분할 노드는 의미 분할 또는 인스턴스 분할 마스크를 발행할 수 있다. 깊이 노드는 밀집 깊이 영상(Dense Depth Image)이나 포인트 클라우드(Point Cloud)를 생성할 수 있고, 3차원 검출 노드는 객체의 위치, 크기, 방향을 발행할 수 있다. 자세 추정 노드는 완전한 6자유도 변환(6DoF Transformation)을 제공할 수 있다. 표준화된 출력은 하위 모듈이 내부 신경망 아키텍처에 의존하지 않고 인지 결과를 사용할 수 있도록 한다.

ROS2 서비스 품질(Quality of Service, QoS) 설정은 고속 카메라 파이프라인에서 특히 중요하다. 영상은 크기가 크고 지속적으로 생성되므로 모든 프레임을 보존하려고 하면 처리 속도가 입력 속도를 따라가지 못할 때 큐(Queue)가 누적되고 지연시간(Latency)이 빠르게 증가할 수 있다. 실시간 인지에서는 모든 과거 프레임을 처리하는 것보다 가장 최신의 유효한 관측을 수신하는 것이 더 중요한 경우가 많다. 따라서 큐 깊이(Queue Depth), 신뢰성(Reliability), 내구성(Durability), 이력 정책(History Policy)은 각 토픽의 의미와 시간 요구사항에 맞게 선택해야 한다.

느린 신경망 추론 노드(Neural Inference Node)는 카메라가 훨씬 높은 프레임 속도로 영상을 발행하더라도 지연시간 병목(Latency Bottleneck)이 될 수 있다. 입력 프레임이 큐에 계속 누적되면 검출기는 결국 이미 오래된 세계 상태를 나타내는 영상을 처리하게 된다. 따라서 실시간 로봇 파이프라인은 센서에서 결과까지의 지연시간(Sensor-to-Result Latency)을 측정하고 버퍼링(Buffering)을 명시적으로 제어해야 한다. 내비게이션 결정이 최신 환경 관측에 의존하는 경우 무제한적인 큐 증가보다 프레임 드롭(Frame Dropping)이 더 적절할 수 있다.

ROS2 프로세스 내부 통신(Intra-Process Communication)은 호환 가능한 노드가 동일한 프로세스에서 실행될 때 불필요한 데이터 이동을 줄일 수 있다. 대용량 카메라 영상과 포인트 클라우드는 반복적인 직렬화(Serialization), 복사, 전송, 역직렬화(Deserialization), 메모리 할당에 높은 비용이 발생한다. 노드 컴포지션(Node Composition)을 이용하면 노드 수준의 모듈성을 유지하면서 여러 처리 구성요소를 하나의 프로세스에서 실행할 수 있다. 적절한 미들웨어(Middleware) 및 메모리 관리 전략과 결합하면 고대역폭 인지 파이프라인에서 지연시간과 CPU 부하를 줄일 수 있다.

노드 컴포지션은 카메라 전처리, 신경망 추론, 즉각적인 후처리(Postprocessing)가 대용량 텐서 또는 영상을 높은 빈도로 교환해야 할 때 특히 유용하다. 그러나 모든 인지 기능을 하나의 프로세스에 배치하면 장애 격리(Fault Isolation)가 감소한다. 하나의 구성요소에서 발생한 충돌이 동일 프로세스를 공유하는 다른 구성요소에도 영향을 줄 수 있다. 따라서 시스템 아키텍처는 완전 분산 또는 완전 통합 방식 중 하나를 무조건 선택하기보다 통신 효율성, 격리성, 유지보수성, 재시작 동작, 안전 요구사항 사이의 균형을 고려해야 한다.

수명주기 관리(Lifecycle Management)는 인지 구성요소를 설정하고 활성화하는 구조화된 방법을 제공한다. 카메라 또는 추론 노드는 처리를 시작하기 전에 보정 파일, 신경망 가중치, TensorRT 엔진, 기준 모델(Reference Model), GPU 자원 등을 로드해야 할 수 있다. 수명주기 상태(Lifecycle State)를 이용하면 데이터 흐름이 활성화되기 전에 초기화를 완료하고, 제어된 비활성화, 정리(Cleanup), 재시작을 수행할 수 있다. 이는 상호 의존적인 여러 인지 구성요소가 예측 가능한 순서로 운용 상태에 진입해야 하는 로봇에서 유용하다.

파라미터(Parameter)는 소프트웨어를 다시 컴파일하지 않고 카메라와 인지 동작을 설정할 수 있는 실용적인 방법을 제공한다. 영상 해상도, 프레임 속도, 신뢰도 임계값(Confidence Threshold), 모델 경로, 깊이 범위, 분할 클래스, 추론 정밀도(Inference Precision), 추적 파라미터 등을 ROS2 파라미터로 제공할 수 있다. 설정 파일(Configuration File)은 서로 다른 로봇의 플랫폼별 설정을 정의할 수 있다. 기하학적 해석에 직접 영향을 주는 파라미터는 특히 신중하게 검증해야 하며, 해상도, 보정 또는 좌표계 가정이 일치하지 않으면 인지 출력이 눈에 띄지 않게 손상될 수 있다.

좌표 프레임 관리(Coordinate-Frame Management)는 영상 인지가 공간 인지(Spatial Perception)로 확장될 때 핵심적인 요소이다. 카메라 광학 프레임(Camera Optical Frame), 카메라 장착 프레임(Camera Mounting Frame), 로봇 베이스 프레임(Robot Base Frame), 오도메트리 프레임(Odometry Frame), 지도 프레임(Map Frame)은 서로 다른 기하학적 기준을 나타낸다. ROS2 TF 인프라는 이러한 프레임 사이의 변환을 관리할 수 있다. 따라서 카메라 프레임에서 추정된 3차원 검출 또는 6DoF 객체 자세를 카메라 외부 보정과 로봇 위치추정 변환이 정확하고 시간적으로 일관된 경우 로봇 베이스 또는 지도 프레임으로 변환할 수 있다.

타임스탬프 일관성(Timestamp Consistency)은 좌표 일관성만큼 중요하다. 로봇이 움직이는 상황에서 잘못된 시간의 좌표 변환을 조회하면 정확한 카메라 검출 결과도 월드 좌표계에서 잘못된 위치에 배치될 수 있다. 인지 메시지는 영상 획득 타임스탬프를 유지해야 하며 TF 조회도 가능한 경우 해당 타임스탬프에 맞춰 수행해야 한다. 관측 시점을 고려하지 않고 가장 최신의 변환만 사용하는 방식은 특히 빠르게 이동하는 플랫폼이나 추론 지연시간이 큰 시스템에서 체계적인 공간 오차를 발생시킬 수 있다.

여러 센서 스트림을 사용하는 인지에서는 동기화(Synchronization)가 더욱 복잡해진다. 스테레오 깊이는 거의 동일한 시점을 나타내는 좌우 영상이 필요하며, 카메라-LiDAR 융합은 서로 다른 센서 주기에도 불구하고 측정값을 시간적으로 연관해야 한다. 다중 카메라 BEV 시스템 역시 여러 시점에서 일관된 관측에 의존한다. ROS2 메시지 동기화(Message Synchronization) 메커니즘을 이용하여 타임스탬프 기준으로 스트림을 연결할 수 있지만, 정밀한 기하 융합이 필요한 경우 하드웨어 동기화(Hardware Synchronization)와 정확한 센서 클록(Sensor Clock)이 더 바람직하다.

카메라 파이프라인에서는 원시 센서 데이터(Raw Sensor Data), 처리된 관측(Processed Observation), 융합 인지 결과(Fused Perception Product)를 명확하게 구분해야 한다. 원시 영상은 원래 측정값을 보존하고, 정류 영상(Rectified Image)은 기하학적으로 보정된 입력을 제공하며, 신경망 출력은 작업별 예측 결과를 나타내고, 융합 객체 또는 점유 표현(Occupancy Representation)은 여러 정보원을 결합한다. 명확한 토픽 이름과 메시지 정의는 하위 소프트웨어가 서로 다른 좌표 프레임, 타임스탬프, 보정 상태 또는 호환되지 않는 표현을 실수로 혼합하는 것을 방지한다.

객체 검출(Object Detection)은 보다 광범위한 카메라 인지 그래프에서 첫 번째 의미 처리 단계(Semantic Stage)로 사용할 수 있다. 검출기는 인스턴스 분할, 깊이 추정, 객체 추적 또는 6DoF 자세 추정에서 사용할 관심 영역(Region of Interest)을 생성할 수 있다. 반대로 여러 알고리즘이 동일한 영상을 독립적으로 처리한 뒤 결과를 융합할 수도 있다. 모든 비전 작업을 반드시 순차적인 파이프라인으로 구성하기보다 실제 계산 의존 관계에 따라 아키텍처를 설계해야 하며, 불필요한 직렬 처리는 지연시간과 장애 전파를 모두 증가시킨다.

깊이 처리(Depth Processing)는 표준화된 인터페이스의 중요성을 잘 보여준다. 단안 또는 스테레오 알고리즘은 내부적으로 완전히 다른 방법을 사용할 수 있지만 두 방식 모두 하위 포인트 클라우드 또는 장애물 처리 노드가 이해할 수 있는 깊이 표현을 발행할 수 있다. 단위, 유효하지 않은 값(Invalid Value), 불확실성, 좌표 규칙, 보정 가정을 명확하게 정의한다면 전체 내비게이션 시스템을 다시 설계하지 않고도 SGBM을 RAFT-Stereo 또는 단안 깊이 모델로 교체할 수 있다.

광학 흐름과 객체 추적처럼 시간 정보를 사용하는 알고리즘은 순서가 유지된 영상 시퀀스와 신뢰할 수 있는 타임스탬프를 필요로 한다. 프레임 손실(Dropped Frame)이 반드시 치명적인 것은 아니지만 처리 노드는 관측 사이의 실제 시간 간격을 알고 있어야 한다. 객체 속도는 가정된 프레임 속도가 아니라 영상 획득 시간을 기준으로 계산해야 한다. GPU 부하로 인해 추론 시간이 변동하는 경우에도 비동기 처리(Asynchronous Processing)는 각 출력 결과와 해당 결과를 생성한 원본 프레임 사이의 관계를 유지해야 한다.

다중 카메라 BEV 인지(Multi-Camera BEV Perception)는 여러 영상 스트림, 보정 파라미터, 자기 운동(Ego-Motion) 정보, 시간적 특징을 조정해야 하므로 ROS2 통합에 추가적인 요구사항을 부여한다. BEV 노드는 필요한 모든 카메라 토픽을 직접 구독하거나 중간 구성요소에서 동기화된 데이터 묶음(Synchronized Bundle)을 전달받을 수 있다. 출력에는 3차원 객체, 의미 BEV 격자(Semantic BEV Grid), 점유 표현 등이 포함될 수 있다. 가능한 경우 하위 경로 계획 시스템은 모델 내부의 특징 텐서가 아니라 명확하게 정의된 이러한 출력 인터페이스에 의존해야 한다.

여러 신경망 인지 노드가 동시에 실행되는 경우 GPU 자원 관리(GPU Resource Management)가 중요해진다. 객체 검출, 분할, 깊이 추정, 광학 흐름, BEV 인지, 자세 추정은 각각 GPU 메모리와 연산 자원을 놓고 경쟁할 수 있다. 모든 네트워크를 최대 카메라 프레임 속도로 독립적으로 실행하면 예측하기 어려운 지연시간이나 메모리 부족이 발생할 수 있다. 따라서 인지 아키텍처는 실제 로봇 기능 요구사항에 따라 실행 주기, GPU 자원, 모델 정밀도, 스케줄링 우선순위(Scheduling Priority)를 할당해야 한다.

모든 인지 작업이 동일한 주기로 실행될 필요는 없다. 빠른 장애물 검출은 높은 갱신 주기를 요구할 수 있지만 의미 지도 작성(Semantic Mapping)이나 정밀 객체 자세 추정은 더 낮은 주기로 실행하거나 특정 작업에서만 트리거(Trigger)할 수 있다. 이벤트 기반 처리(Event-Driven Processing)는 관련 객체나 운용 상태가 감지된 경우에만 계산 비용이 높은 알고리즘을 활성화하여 연산 부하를 줄일 수 있다. 이를 통해 내비게이션, 조작 또는 검사에서 현재 필요한 기능에 따라 연산 자원을 배분하는 계층적 인지 파이프라인(Hierarchical Perception Pipeline)을 구성할 수 있다.

진단(Diagnostics)은 외부 디버깅 기능이 아니라 인지 아키텍처의 일부로 다루어야 한다. 노드는 카메라 프레임 속도, 손실 프레임, 추론 지연시간, GPU 사용률, 동기화 오차, 보정 상태, 출력 신뢰도, 토픽 최신성(Topic Freshness)을 모니터링할 수 있다. 로봇은 실제 환경에 검출된 객체가 없는 상황과 인지 파이프라인이 유효한 결과 생성을 중단한 상황을 구분할 수 있어야 한다. 따라서 상태 정보(Health Information)는 장애 처리와 성능 저하 운용 모드(Degraded Operating Mode)를 지원할 수 있다.

장애 관리(Failure Management)는 안전과 관련된 로봇 인지에서 특히 중요하다. 카메라는 연결이 끊기거나 가려지거나 과다 노출(Overexposure)되거나 물리적으로 위치가 변할 수 있으며, 신경망 노드는 GPU 메모리 부족이나 손상된 입력으로 인해 실패할 수 있다. 감시기(Watchdog)와 수명주기 감독(Lifecycle Supervision)은 출력이 누락되거나 오래된 상태를 감지하고 제어된 복구를 시도할 수 있다. 하위 모듈은 인지 기능을 사용할 수 없을 때 감속, 센서 전환, 운용 제한 또는 시스템 요구사항에 따른 안전 상태(Safe State) 진입과 같은 동작을 정의해야 한다.

ROS2 기록 및 재생(Recording and Replay)은 인지 개발과 검증을 위한 중요한 기반을 제공한다. 카메라 영상, 보정 메시지, 좌표 변환, 오도메트리(Odometry), 검출 결과, 깊이 지도, 진단 정보를 함께 기록하면 동일한 센서 시퀀스를 이후 수정된 알고리즘에 다시 재생할 수 있다. 이를 통해 알고리즘 평가를 실제 로봇의 가용성과 분리할 수 있으며 모델, 파라미터, 미들웨어 설정 또는 하드웨어 플랫폼이 변경될 때 회귀 시험(Regression Testing)을 수행할 수 있다.

성능 평가(Performance Evaluation)는 신경망 추론만이 아니라 전체 파이프라인을 측정해야 한다. 카메라 노출 및 영상 획득, 드라이버 버퍼링, 메시지 전송, 전처리, GPU 전송, 추론, 디코딩, 좌표 변환, 융합, 결과 발행이 모두 경로 계획 모듈이 실제로 사용할 수 있는 인지 결과를 얻기까지의 시간에 영향을 준다. 로봇에서는 신경망 모델만을 대상으로 측정한 초당 프레임 수(Frames Per Second)보다 센서-행동 지연시간(Sensor-to-Action Latency)과 지연시간 변동(Latency Variation)이 더 의미 있는 성능 지표가 될 수 있다.

잘 설계된 ROS2 카메라 인지 아키텍처는 영상 획득과 점차 고도화되는 공간 이해(Spatial Understanding)를 연결하는 통합 계층(Integration Layer)의 역할을 한다. 객체 검출, 분할, 단안 및 스테레오 깊이, BEV 인지, 3차원 객체 검출, 광학 흐름, 장면 흐름(Scene Flow), 6DoF 자세 추정은 각각 전문화된 모듈로 유지하면서 타임스탬프, 보정 정보, 좌표 프레임, 진단 정보, 표준화된 인터페이스를 공유할 수 있다. 이러한 모듈식 구조는 개별 카메라 알고리즘을 내비게이션, 조작, 검사, 객체 추적 및 보다 광범위한 피지컬 AI(Physical AI) 동작을 지원하는 유지보수 가능한 인지 하위 시스템(Perception Subsystem)으로 통합한다.

## 02.10. Camera Perception Quantization for Edge Deploy [w/Code]

![](images/image10.png){width="7.268055555555556in" height="7.268055555555556in"}

양자화(Quantization)는 GPU 메모리, 전력, 대역폭, 추론 지연시간(Inference Latency)이 제한된 엣지 컴퓨팅 플랫폼(Edge Computing Platform)에 카메라 인지 모델(Camera Perception Model)을 배포하기 위한 핵심 최적화 기술이다. 모든 신경망 연산을 고정밀 부동소수점(High-Precision Floating-Point) 값으로 수행하는 대신, 양자화는 가중치(Weight)와 활성값(Activation)을 더 낮은 정밀도의 형식으로 표현한다. 로봇 인지에서의 목표는 단순히 연산량을 줄이는 것이 아니라 실시간 시스템 제약을 만족하면서 신뢰할 수 있는 시각적 이해 능력을 유지하는 것이다.

카메라 인지 워크로드(Camera Perception Workload)는 객체 검출(Object Detection), 분할(Segmentation), 단안 깊이 추정(Monocular Depth Estimation), 스테레오 정합(Stereo Matching), 조감도 인지(BEV Perception), 광학 흐름(Optical Flow), 자세 추정(Pose Estimation) 등 여러 신경망을 동시에 실행하는 경우가 많다. 모든 모델을 FP32로 실행하면 상당한 메모리와 연산 자원을 소비할 수 있다. 저정밀 추론(Lower-Precision Inference)을 사용하면 제한된 전력 범위 안에서 동일한 엣지 프로세서가 더 많은 인지 기능, 더 높은 영상 처리 속도 또는 추가적인 경로 계획 및 제어 워크로드를 지원할 수 있다.

FP32는 32비트 부동소수점(32-Bit Floating-Point) 값을 사용하며 일반적으로 모델 개발과 정확도 평가를 위한 편리한 기준선(Baseline)을 제공한다. FP16은 수치 정밀도를 낮추면서도 신경망 추론에 적합한 비교적 넓은 동적 범위(Dynamic Range)를 유지한다. 최신 GPU는 특화된 텐서 처리 하드웨어(Tensor-Processing Hardware)를 이용하여 FP16 연산을 효율적으로 실행할 수 있다. 따라서 많은 카메라 인지 네트워크에서 FP16은 예측 품질의 변화가 비교적 작으면서 상당한 가속 효과를 제공하기 때문에 첫 번째 실용적 최적화 단계가 된다.

INT8 양자화(INT8 Quantization)는 많은 신경망 값을 8비트 정수로 표현하며 FP16보다 메모리 트래픽과 연산 비용을 더 크게 줄일 수 있다. 부동소수점 텐서(Floating-Point Tensor)는 스케일(Scale)과 표현 방식에 따라 영점(Zero Point) 등의 양자화 파라미터를 이용하여 정수 범위로 매핑된다. 추론 과정에서는 최적화된 커널(Kernel)이 정수 또는 혼합 정밀도(Mixed Precision) 연산을 수행하면서 양자화된 값과 원래 부동소수점 표현 사이의 수치적 관계를 유지한다.

기본적인 양자화 매핑(Quantization Mapping)은 부동소수점 값을 이산적인 정수 표현(Discrete Integer Representation)으로 변환하는 과정으로 이해할 수 있다. 스케일은 하나의 정수 단계가 실제 수치 범위에서 어느 정도의 크기를 표현하는지를 결정하며, 영점은 특정 실수 값을 정수 코드에 정렬한다. 대칭 양자화(Symmetric Quantization)는 일반적으로 표현 범위를 0을 중심으로 구성하는 반면, 비대칭 양자화(Asymmetric Quantization)는 표현 가능한 구간을 이동시킬 수 있다. 최적의 방식은 텐서 분포와 하드웨어 지원에 따라 달라진다.

가중치와 활성값은 양자화 과정에서 서로 다른 특성을 가진다. 신경망 가중치는 학습 이후 고정되므로 오프라인에서 분석할 수 있지만, 활성값 분포(Activation Distribution)는 실제 입력 영상과 네트워크 내부 동작에 따라 달라진다. 따라서 활성값 양자화는 실제 운용 데이터에 더욱 민감한 경우가 많다. 강한 햇빛, 어두운 실내 환경, 카메라 잡음, 특이한 질감, 날씨 또는 센서 노출 변화는 모델 개발 과정에서 관측한 것과 다른 활성값 분포를 발생시킬 수 있다.

텐서 단위 양자화(Per-Tensor Quantization)는 전체 텐서에 하나의 스케일을 사용하여 단순하고 효율적인 표현을 제공한다. 채널 단위 양자화(Per-Channel Quantization)는 개별 채널에 서로 다른 스케일을 할당하여 필터 사이의 가중치 크기 차이에 양자화기가 적응할 수 있도록 한다. 채널 단위 가중치 양자화는 합성곱 신경망(Convolutional Network)에서 양자화 오차를 상당히 줄일 수 있다. 그러나 실제 표현 방식은 대상 추론 엔진(Target Inference Engine)이 지원하는 기능과 최적화 경로에 맞춰 선택해야 한다.

학습 후 양자화(Post-Training Quantization, PTQ)는 이미 학습된 부동소수점 모델을 완전한 재학습 없이 저정밀 표현으로 변환한다. 따라서 기존 모델을 비교적 빠르게 최적화할 수 있어 엣지 배포에서 매력적인 방법이다. FP16 변환은 추가적인 데이터가 거의 필요하지 않을 수 있지만 INT8 PTQ는 일반적으로 적절한 활성값 범위를 결정하기 위한 보정 데이터(Calibration Data)가 필요하다. 이후 변환된 모델은 원래의 부동소수점 기준 모델과 비교하여 평가해야 한다.

보정(Calibration)은 INT8 PTQ에서 가장 중요한 단계 중 하나이다. 대표성을 가진 카메라 영상 집합을 네트워크에 입력하여 추론 프레임워크가 활성값 통계(Activation Statistics)를 관측하도록 한다. 이러한 통계는 양자화 범위와 스케일을 선택하는 데 사용된다. 보정 데이터는 예상되는 환경, 객체 크기, 조명, 카메라 시점, 날씨, 실내 또는 실외 운용 등 인지 입력 분포에 실질적인 영향을 주는 실제 배포 조건을 대표해야 한다.

보정 데이터셋(Calibration Dataset)은 반드시 원래 학습 데이터셋만큼 클 필요는 없지만 임의적인 샘플 수보다 대표성(Representativeness)이 중요하다. 야간에도 운용되는 실외 로봇의 보정에 밝은 주간 영상만 사용하면 부적절한 활성값 범위가 생성될 수 있다. 마찬가지로 복잡하지 않은 장면만으로 보정하면 밀집된 산업 환경을 충분히 표현하지 못할 수 있다. 따라서 보정 데이터는 단순한 무작위 영상 집합이 아니라 운용 설계 영역(Operational Design Domain)을 공학적으로 대표하는 데이터로 다루어야 한다.

이상치(Outlier)는 소수의 비정상적으로 큰 활성값이 양자화 범위를 확장하여 대부분의 값에 사용할 수 있는 유효 해상도를 감소시키기 때문에 중요한 양자화 문제를 발생시킨다. 보정 알고리즘은 포화(Saturation)와 양자화 해상도 사이의 균형을 맞추기 위해 통계적 방법으로 클리핑 범위(Clipping Range)를 선택할 수 있다. 최적의 전략은 네트워크, 계층 동작, 추론 프레임워크, 대상 작업에 따라 달라지므로 하나의 보정 방법이 항상 최적이라고 가정하지 말고 실제 정확도를 측정해야 한다.

양자화 인식 학습(Quantization-Aware Training, QAT)은 모델 학습 또는 미세조정(Fine-Tuning) 과정에 모의 양자화 효과(Simulated Quantization Effect)를 포함한다. 이를 통해 네트워크는 감소된 수치 정밀도에 더 강인한 파라미터를 학습한다. 특히 민감한 아키텍처나 출력에서 INT8 PTQ로 인한 성능 저하가 허용 범위를 초과할 경우 QAT를 통해 정확도를 회복할 수 있다. 그러나 추가적인 학습 과정이 필요하므로 일반적으로 더 단순한 PTQ 방법을 먼저 응용 요구사항과 비교 평가한 이후 고려하는 것이 적절하다.

서로 다른 카메라 인지 작업은 수치 정밀도에 대한 민감도가 다르다. 분류(Classification)나 대략적인 객체 검출은 INT8 양자화를 비교적 잘 허용할 수 있지만 밀집 깊이 추정(Dense Depth Estimation), 광학 흐름, 정밀한 분할 경계, 스테레오 시차(Stereo Disparity), 정밀 6DoF 자세 추정은 더욱 민감할 수 있다. 중간 특징에서 발생한 작은 수치 오차가 최종 출력에서는 공간적 또는 기하학적 오차로 확대될 수 있다. 따라서 각 인지 작업의 의미와 특성에 따라 양자화 정책을 선택해야 한다.

혼합 정밀도 추론(Mixed-Precision Inference)은 전체 INT8 변환으로 허용할 수 없는 오차가 발생할 때 실용적인 절충안을 제공한다. 양자화에 강한 계층은 INT8로 실행하고 민감한 연산은 FP16 또는 다른 지원 정밀도로 유지할 수 있다. 입력 정규화(Input Normalization), 어텐션 연산(Attention Operation), 회귀 헤드(Regression Head), 기하 예측 계층(Geometric Prediction Layer), 특수 연산자는 아키텍처에 따라 더 높은 정밀도가 필요할 수 있다. 최적화된 엔진은 사용 가능한 커널과 정밀도 제약에 따라 실행을 분할할 수 있다.

객체 검출에서는 양자화 이후 전체 평균 정밀도(Mean Average Precision)만 확인해서는 충분하지 않다. 로봇은 작은 객체 검출, 신뢰도 보정(Confidence Calibration), 위치 정확도 또는 특정 안전 중요 클래스(Safety-Critical Class)에 크게 의존할 수 있다. INT8 모델은 전체 평균 정확도 저하는 작으면서도 먼 거리의 보행자나 부분적으로 가려진 장애물의 성능이 크게 감소할 수 있다. 따라서 하나의 종합 지표만 사용하는 대신 클래스, 거리, 객체 크기, 시나리오별 성능을 평가해야 한다.

깊이 추정(Depth Estimation)은 수치 변화가 미터 단위 기하 구조에 영향을 줄 수 있기 때문에 특별한 주의가 필요하다. 작은 네트워크 출력 차이도 깊이 스케일링(Depth Scaling)이나 재구성 이후 의미 있는 공간 오차로 변환될 수 있다. 근거리, 중거리, 장거리 영역에서 절대 및 상대 깊이 오차를 평가해야 한다. 내비게이션에서는 일반적인 영상 수준 지표보다 장애물 거리와 자유 공간 추정(Free-Space Estimation)에 미치는 영향이 더 중요할 수 있다. 따라서 양자화는 하위 기하학적 결과까지 검증해야 한다.

스테레오 네트워크(Stereo Network)는 시차 정확도가 깊이 정확도와 밀접하게 연결되기 때문에 추가적인 민감성을 가진다. 장거리에서는 작은 시차 오차도 큰 깊이 오차로 변환될 수 있다. 따라서 특징 추출, 상관 관계(Correlation), 비용 볼륨 처리(Cost-Volume Processing), 시차 회귀(Disparity Regression)의 양자화는 실제 운용 거리 범위에서 시험해야 한다. 근거리 창고 내비게이션에서 허용되는 설정이라도 평균 벤치마크 정확도가 유사하더라도 장거리 실외 인지에는 충분한 기하학적 정밀도를 제공하지 못할 수 있다.

광학 흐름과 장면 흐름(Scene Flow) 네트워크 역시 움직임 중심의 평가가 필요하다. 양자화 오차는 특히 얇은 구조물, 객체 경계, 질감이 약한 영역, 작은 이동 객체에서 대응 관계 품질에 영향을 줄 수 있다. 종점 오차(Endpoint Error)는 하위 속도 추정, 동적 마스크(Dynamic Mask), 객체 추적 동작과 함께 평가해야 한다. 로봇에서는 넓은 정적 영상 영역의 평균 오차를 최소화하는 것보다 작은 횡단 객체의 움직임을 정확하게 보존하는 것이 더 중요할 수 있다.

트랜스포머 기반 카메라 인지 모델(Transformer-Based Camera Perception Model)은 어텐션 계층, 정규화(Normalization), 행렬 곱셈(Matrix Multiplication), 활성값 분포가 저정밀 연산에 서로 다르게 반응할 수 있기 때문에 추가적인 고려가 필요하다. BEV 인지는 여러 카메라, 공간 위치, 시간에 걸쳐 정보를 집계한다. 따라서 양자화 오차가 여러 단계를 거쳐 전파된 후 최종 공간 표현에 영향을 줄 수 있다. 특정 트랜스포머 구성요소가 합성곱 계층보다 훨씬 민감한 경우 혼합 정밀도가 적절할 수 있다.

TensorRT와 같은 최적화 추론 런타임(Optimized Inference Runtime)은 학습된 네트워크를 하드웨어 특화 실행 엔진(Hardware-Specific Execution Engine)으로 컴파일할 수 있다. 엔진 생성 과정에서 지원되는 계층에는 플랫폼 기능과 최적화 제약에 따라 효율적인 커널과 수치 정밀도를 할당할 수 있다. FP16과 INT8 실행은 추론 지연시간과 메모리 사용량을 줄일 수 있지만 지원되지 않는 연산자는 더 높은 정밀도로 폴백(Fallback)하거나 그래프 수정이 필요할 수 있다. 따라서 예상되는 가속 효과를 검증할 때 실제 생성된 엔진을 확인하는 것이 중요하다.

엣지 배포 성능은 모델 실행 시간만이 아니라 종단간 지연시간(End-to-End Latency)을 기준으로 평가해야 한다. 영상 획득, 전처리, 메모리 전송, 추론, 디코딩, 좌표 변환, 객체 추적, ROS2 발행이 모두 실제로 사용할 수 있는 인지 결과가 생성되기까지의 시간에 영향을 준다. 양자화를 통해 신경망 추론을 크게 가속하더라도 카메라 데이터 전송, CPU 전처리 또는 후처리가 주요 병목이라면 전체 시스템 성능 향상은 제한적일 수 있다.

메모리 감소(Memory Reduction)는 순수한 추론 속도만큼 중요할 수 있다. 저정밀 가중치는 모델 저장 공간과 GPU 메모리 소비를 줄이고, 양자화된 활성값은 중간 메모리 요구량과 메모리 대역폭을 줄일 수 있다. 이를 통해 하나의 엣지 GPU에서 여러 인지 네트워크를 메모리 부족 없이 동시에 실행할 수 있다. 감소된 메모리 부담은 전체 로봇 자율주행 스택에 필요한 더 큰 영상 버퍼, 객체 추적 모듈, BEV 표현, 지도 또는 경로 계획 모델을 위한 공간을 확보할 수도 있다.

전력 및 열 동작(Power and Thermal Behavior) 역시 실제 엣지 플랫폼에서 측정해야 한다. 짧은 벤치마크에서 높은 처리량을 달성하는 인지 모델도 지속적으로 실행하면 열 또는 전력 제한으로 인해 클록 주파수(Clock Frequency)가 감소할 수 있다. 양자화는 계산 효율을 향상시키고 자원 부담을 줄일 수 있지만 실제 임무 시간에 해당하는 지속적인 운용 조건에서 시스템 성능을 시험해야 한다. 따라서 엣지 최적화는 연산, 메모리, 냉각, 전원 공급, 워크로드 스케줄링을 포함하는 플랫폼 수준의 문제이다.

ROS2 통합에서는 내부 모델이 FP32, FP16, INT8 또는 혼합 정밀도로 실행되는지와 관계없이 동일한 외부 인지 인터페이스를 유지해야 한다. 검출 메시지, 깊이 출력, 분할 마스크, 자세, 타임스탬프, 좌표 프레임, 신뢰도 값은 의미적으로 일관되어야 한다. 이러한 추상화(Abstract Interface)를 통해 내비게이션, 조작, 지도 작성, 센서 융합 노드를 다시 설계하지 않고도 개발 단계의 모델을 최적화된 추론 엔진으로 교체할 수 있다.

강인한 배포 워크플로(Robust Deployment Workflow)는 검증된 부동소수점 기준 모델에서 시작하여 지원되는 중간 표현(Intermediate Representation)으로 모델을 변환하고, 최적화된 FP16 또는 INT8 엔진을 생성한 뒤 각 단계를 기준 모델과 비교 평가한다. 정확도, 지연시간, 메모리 사용량, GPU 사용률, 전력, 열 동작을 모두 측정해야 한다. INT8 성능 저하가 지나치다면 보정 데이터를 개선하거나 민감한 계층을 높은 정밀도로 유지하거나 QAT를 적용한 뒤 다시 검증할 수 있다.

회귀 시험(Regression Testing)은 네트워크 아키텍처가 동일하게 보이더라도 모델 최적화 과정에서 수치적 동작이 달라지기 때문에 필수적이다. 기록된 ROS2 카메라 시퀀스를 사용하면 FP32, FP16, INT8, 혼합 정밀도 구현에 동일한 입력을 반복적으로 제공하여 비교할 수 있다. 비교 항목에는 신경망 자체의 성능 지표뿐만 아니라 객체 추적 안정성, 장애물 거리, 객체 자세, 내비게이션 결정, 인지 지연시간과 같은 하위 시스템 동작도 포함해야 한다. 대표적인 어려운 시나리오는 영구적인 검증 데이터셋의 일부로 유지하는 것이 바람직하다.

양자화는 궁극적으로 단순한 모델 압축 스위치가 아니라 시스템 엔지니어링(System Engineering) 관점의 의사결정으로 다루어야 한다. 최적의 정밀도는 실제 대상 하드웨어에서 요구되는 인지 정확도, 기하학적 신뢰성, 지연시간, 메모리, 전력, 안전 제약을 만족하는 가장 낮은 정밀도이다. 따라서 서로 다른 노드는 서로 다른 수치 형식을 사용할 수 있다. 고속 객체 검출기는 INT8로 효율적으로 동작하면서 정밀 깊이, 광학 흐름 또는 자세 추정은 추가적인 수치 정밀도가 실제 로봇 성능에 의미 있는 가치를 제공하는 경우 FP16을 유지할 수 있다.

엣지 배포를 위한 카메라 인지 양자화(Camera Perception Quantization for Edge Deployment)는 신경망 최적화를 로봇 시스템 아키텍처와 직접 연결한다. FP16, INT8, PTQ, QAT, 보정, 혼합 정밀도, 최적화된 추론 엔진, 하드웨어 가속(Hardware Acceleration)은 연산 비용을 줄이는 방법을 제공하지만 그 성공 여부는 전체 인지 파이프라인의 동작을 기준으로 판단해야 한다. 적절하게 검증된 양자화는 신뢰할 수 있는 피지컬 AI(Physical AI)에 필요한 공간적·시간적 정보를 유지하면서 정교한 카메라 인지가 실제적인 엣지 시스템의 자원 제약 안에서 동작할 수 있도록 한다.
