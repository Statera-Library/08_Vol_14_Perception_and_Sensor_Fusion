**Volume 14. Perception and Sensor Fusion**


# Chapter 01. Robot Perception Fundamentals

##  

## 01.01. Robot Perception Pipeline Sense Represent Understand

![](images/image1.png){width="7.268055555555556in" height="7.268055555555556in"}

Robot perception is the process through which a robot converts physical signals from its environment into representations that can support autonomous decisions and actions. A useful abstraction divides this process into three closely connected stages: Sense, Represent, and Understand. These stages form the foundation upon which later functions such as localization, mapping, navigation, manipulation, and Physical AI reasoning are constructed.

The Sense stage establishes the robot\'s direct connection to the physical world. Cameras measure patterns of visible or infrared light, LiDAR measures reflected laser energy and range, radar observes electromagnetic reflections and Doppler velocity, while IMUs measure acceleration and angular velocity. Force, tactile, ultrasonic, GNSS, and other sensors extend perception to contact, proximity, motion, and global position.

Sensor measurements are not equivalent to knowledge about the environment. A camera produces arrays of pixel intensities, a LiDAR produces range measurements that can become three-dimensional points, and an IMU produces time-dependent inertial signals. Each measurement contains useful information but also noise, bias, uncertainty, limited resolution, occlusion, and environmental sensitivity. Perception therefore begins with measurement rather than with an immediately correct description of reality.

Practical sensing pipelines must first make heterogeneous sensor streams computationally usable. Raw measurements are decoded, timestamped, calibrated, filtered, synchronized, and transformed into consistent coordinate frames. Camera distortion may be corrected, LiDAR returns may be filtered, IMU bias may be compensated, and radar detections may require signal processing. Accurate timestamps and extrinsic calibration become particularly important when a moving robot combines measurements from several sensors.

The Represent stage converts processed measurements into structures that algorithms can efficiently interpret. Representation is therefore an intermediate language between physical sensing and machine understanding. Depending on the task, the same environment may be represented as images, depth maps, point clouds, voxels, meshes, occupancy grids, bird\'s-eye-view features, semantic maps, learned embeddings, or scene graphs. The broader volume explicitly develops spatial and semantic representations after this pipeline foundation.

Geometric representations describe where surfaces, obstacles, and objects exist in space. A point cloud preserves measured three-dimensional samples, while voxel structures discretize space into volumetric cells. Occupancy representations estimate whether regions are free, occupied, or unknown, and meshes describe connected surfaces. These representations differ in memory requirements, resolution, update cost, and suitability for downstream tasks, so no single geometric representation is optimal for every robot.

Representation also requires a consistent understanding of coordinate systems. Sensor measurements may initially exist in individual camera, LiDAR, radar, or IMU frames, while navigation normally requires information in robot, odometry, or map frames. Transforming observations between these frames allows measurements collected from different locations and times to describe a common physical environment. Errors in calibration or pose estimation therefore propagate directly into the quality of the resulting representation.

Modern perception increasingly uses learned feature representations in addition to explicit geometry. Neural networks transform pixels, point clouds, radar measurements, or multimodal observations into feature vectors and feature maps that capture patterns useful for detection, segmentation, recognition, or prediction. Such representations can preserve information that is difficult to encode manually and can support semantic reasoning beyond conventional geometric processing.

The Understand stage assigns task-relevant meaning to represented sensor information. Object detection can determine that a region contains a pedestrian, vehicle, pallet, tool, or obstacle, while semantic segmentation assigns categories to pixels or points. Instance segmentation distinguishes individual objects, pose estimation determines their spatial configuration, and tracking associates observations across time. Scene understanding extends these outputs toward relationships, context, and environmental structure.

Understanding is inherently task dependent. An indoor AMR may primarily need to distinguish traversable space, humans, pallets, racks, and dynamic obstacles. A mobile manipulator must additionally reason about object identity, six-degree-of-freedom pose, graspable regions, and contact conditions. A quadruped requires terrain geometry and foothold information, while a UAV needs obstacle, free-space, depth, motion, and landing-zone information. The required perception representation therefore follows the robot\'s intended actions.

Temporal information is essential because robots operate in dynamic environments rather than isolated frames. A single observation may reveal where an object appears, but a sequence can reveal whether it is moving, its estimated velocity, its trajectory, and whether it is likely to intersect the robot\'s path. Tracking, temporal feature aggregation, optical flow, scene flow, inertial integration, and motion estimation convert successive measurements into a continuously evolving interpretation of the environment.

Multi-sensor perception improves this interpretation by combining complementary physical observations. Cameras provide rich appearance and semantic information but depend strongly on illumination and visibility. LiDAR provides direct geometric range measurements, radar contributes robust distance and radial velocity observations, and IMUs provide high-rate motion information. Their combination can reduce individual weaknesses, but fusion requires careful spatial calibration, temporal synchronization, uncertainty handling, and failure detection.

A complete perception pipeline therefore should not be viewed as a simple one-way conversion from raw data to labels. Information can exist simultaneously at several abstraction levels, from raw measurements and geometric primitives to semantic objects and scene-level relationships. Downstream modules may consume different levels directly: obstacle avoidance may use occupancy, manipulation may require object pose, and higher-level Physical AI may consume semantic or learned representations for reasoning.

Uncertainty must accompany this transformation whenever possible. Sensor noise, incomplete observations, ambiguous classifications, occlusions, calibration errors, and domain changes prevent perception outputs from being perfectly certain. Confidence scores, covariance estimates, probabilistic occupancy, multiple hypotheses, and consistency checks allow downstream systems to distinguish strong evidence from weak evidence. This distinction becomes especially important when perception participates directly in safety-critical robot behavior.

Real-time operation introduces another fundamental constraint. Perception must produce sufficiently accurate information before that information becomes obsolete. High-resolution sensing and large neural models can improve representational richness, but they also increase computation, memory traffic, and latency. A production robot therefore balances sensing frequency, representation complexity, model accuracy, hardware capability, and end-to-end latency rather than optimizing perception accuracy independently.

The Sense--Represent--Understand abstraction also clarifies the relationship between classical robotics and modern AI. Classical systems often construct explicit geometric representations before applying carefully defined estimation and recognition algorithms. Learning-based systems may transform sensor measurements directly into latent features and semantic predictions. End-to-end approaches can reduce visible boundaries even further, but sensing, internal representation, and interpretation still exist conceptually within the computational process.

For Physical AI systems, perception ultimately serves action rather than observation alone. A useful perceptual representation must provide information that enables the robot to decide where it can move, what it can manipulate, which entities matter to the task, how the environment is changing, and what uncertainty remains. Later topics in this volume therefore extend the foundation toward 3D scene understanding, occupancy and semantic maps, Physical AI perception, tactile sensing, and open-vocabulary perception.

The overall pipeline can consequently be understood as a progressive transformation of physical evidence. Sense acquires measurements from the world, Represent organizes those measurements into computational structures, and Understand extracts meaning relevant to the robot\'s objectives. Reliable autonomous behavior emerges when these stages remain geometrically consistent, temporally aligned, uncertainty-aware, computationally efficient, and tightly connected to the decisions and actions that follow perception.

로봇 인지(Robot Perception)는 로봇이 물리적 환경(Physical Environment)에서 획득한 신호를 자율적인 판단과 행동을 지원할 수 있는 표현(Representation)으로 변환하는 과정이다. 이를 이해하기 위한 유용한 추상화는 감지(Sense), 표현(Represent), 이해(Understand)의 세 단계로 구분하는 것이다. 이러한 단계는 이후의 위치추정(Localization), 매핑(Mapping), 내비게이션(Navigation), 조작(Manipulation), 피지컬 AI(Physical AI) 추론의 기반을 형성한다.

감지(Sense) 단계는 로봇과 물리적 세계 사이의 직접적인 연결을 형성한다. 카메라(Camera)는 가시광선 또는 적외선의 패턴을 측정하고, 라이다(LiDAR)는 반사된 레이저 에너지와 거리를 측정하며, 레이더(Radar)는 전자기파의 반사와 도플러 속도(Doppler Velocity)를 관측한다. 관성측정장치(IMU)는 가속도와 각속도를 측정하며, 힘(Force), 촉각(Tactile), 초음파(Ultrasonic), 위성항법시스템(GNSS) 등의 센서는 접촉, 근접성, 운동 및 전역 위치에 대한 인지 범위를 확장한다.

센서 측정값(Sensor Measurement)은 환경에 대한 지식 그 자체와 동일하지 않다. 카메라는 픽셀 강도(Pixel Intensity)의 배열을 생성하고, 라이다는 3차원 점으로 변환할 수 있는 거리 측정값을 생성하며, 관성측정장치(IMU)는 시간에 따라 변화하는 관성 신호를 생성한다. 각각의 측정값은 유용한 정보를 포함하지만 잡음(Noise), 바이어스(Bias), 불확실성(Uncertainty), 제한된 해상도, 가림(Occlusion), 환경 민감성도 함께 포함한다. 따라서 인지는 즉시 정확한 현실의 설명에서 시작되는 것이 아니라 측정에서 시작된다.

실제 감지 파이프라인(Sensing Pipeline)은 먼저 서로 다른 종류의 센서 스트림(Sensor Stream)을 계산 가능한 형태로 만들어야 한다. 원시 측정값(Raw Measurement)은 디코딩(Decoding), 타임스탬프(Timestamp) 부여, 보정(Calibration), 필터링(Filtering), 동기화(Synchronization)를 거쳐 일관된 좌표계(Coordinate Frame)로 변환된다. 카메라 왜곡을 보정하고, 라이다 반사점을 필터링하며, IMU 바이어스를 보상하고, 레이더 검출값에 신호처리(Signal Processing)를 적용할 수 있다. 움직이는 로봇이 여러 센서의 측정값을 결합할 때에는 정확한 시간 정보와 외부 파라미터 보정(Extrinsic Calibration)이 특히 중요하다.

표현(Represent) 단계는 처리된 측정값을 알고리즘이 효율적으로 해석할 수 있는 구조로 변환한다. 따라서 표현은 물리적 감지와 기계적 이해 사이를 연결하는 중간 언어(Intermediate Language)라고 볼 수 있다. 작업에 따라 동일한 환경도 이미지(Image), 깊이맵(Depth Map), 포인트 클라우드(Point Cloud), 복셀(Voxel), 메시(Mesh), 점유 격자(Occupancy Grid), 조감도 특징(Bird\'s-Eye-View Feature), 의미 지도(Semantic Map), 학습 임베딩(Learned Embedding), 장면 그래프(Scene Graph) 등으로 표현할 수 있다.

기하학적 표현(Geometric Representation)은 공간에서 표면, 장애물, 객체가 어디에 존재하는지를 나타낸다. 포인트 클라우드(Point Cloud)는 측정된 3차원 샘플을 유지하고, 복셀(Voxel) 구조는 공간을 체적 셀(Volumetric Cell)로 이산화한다. 점유 표현(Occupancy Representation)은 공간 영역이 자유 공간(Free), 점유 공간(Occupied), 미확인 공간(Unknown) 중 어느 상태인지 추정하며, 메시(Mesh)는 연결된 표면을 표현한다. 이러한 표현은 메모리 요구량, 해상도, 갱신 비용 및 후속 작업에 대한 적합성이 서로 다르므로 모든 로봇에 최적인 단일 기하학적 표현은 존재하지 않는다.

표현 과정에서는 좌표계(Coordinate System)에 대한 일관된 이해도 필요하다. 센서 측정값은 처음에는 개별 카메라, 라이다, 레이더 또는 IMU 좌표계에 존재하지만, 내비게이션(Navigation)은 일반적으로 로봇(Robot), 오도메트리(Odometry) 또는 지도(Map) 좌표계의 정보를 필요로 한다. 관측값을 이러한 좌표계 사이에서 변환하면 서로 다른 위치와 시간에 수집된 측정값으로 공통된 물리적 환경을 기술할 수 있다. 따라서 보정 또는 자세 추정(Pose Estimation)의 오차는 최종 표현의 품질에 직접적으로 전파된다.

현대의 인지(Perception)는 명시적인 기하학 정보뿐 아니라 학습된 특징 표현(Learned Feature Representation)을 점점 더 많이 사용한다. 신경망(Neural Network)은 픽셀, 포인트 클라우드, 레이더 측정값 또는 다중모달 관측(Multimodal Observation)을 객체 검출, 분할, 인식 또는 예측에 유용한 패턴을 포함하는 특징 벡터(Feature Vector)와 특징 맵(Feature Map)으로 변환한다. 이러한 표현은 수작업으로 정의하기 어려운 정보를 보존할 수 있으며 전통적인 기하학 처리보다 높은 수준의 의미론적 추론(Semantic Reasoning)을 지원할 수 있다.

이해(Understand) 단계에서는 표현된 센서 정보에 작업과 관련된 의미(Task-Relevant Meaning)를 부여한다. 객체 검출(Object Detection)은 특정 영역에 보행자, 차량, 팔레트, 도구 또는 장애물이 존재하는지를 판단할 수 있으며, 의미론적 분할(Semantic Segmentation)은 픽셀이나 점에 범주를 할당한다. 인스턴스 분할(Instance Segmentation)은 개별 객체를 구분하고, 자세 추정(Pose Estimation)은 객체의 공간적 구성을 결정하며, 추적(Tracking)은 시간에 따른 관측값을 연결한다. 장면 이해(Scene Understanding)는 이러한 결과를 객체 관계, 상황적 맥락(Context), 환경 구조에 대한 이해로 확장한다.

이해(Understanding)는 본질적으로 작업 의존적(Task-Dependent)이다. 실내 자율이동로봇(AMR)은 주행 가능 공간, 사람, 팔레트, 랙(Rack), 동적 장애물을 주로 구분해야 한다. 이동형 매니퓰레이터(Mobile Manipulator)는 여기에 객체 식별, 6자유도 자세(6DoF Pose), 파지 가능 영역(Graspable Region), 접촉 상태(Contact Condition)를 추가로 이해해야 한다. 사족보행 로봇(Quadruped)은 지형 형상과 발 디딤 위치(Foothold)가 필요하며, 무인항공기(UAV)는 장애물, 자유 공간, 깊이, 움직임 및 착륙 영역에 관한 정보가 필요하다. 따라서 필요한 인지 표현은 로봇이 수행하려는 행동에 따라 결정된다.

로봇은 정적인 단일 프레임이 아니라 동적인 환경에서 작동하기 때문에 시간 정보(Temporal Information)가 필수적이다. 하나의 관측은 객체가 어디에 나타났는지를 알려줄 수 있지만, 연속된 관측은 객체의 움직임 여부, 추정 속도, 이동 궤적, 그리고 로봇의 경로와 충돌할 가능성을 알려줄 수 있다. 추적(Tracking), 시간 특징 집계(Temporal Feature Aggregation), 광학 흐름(Optical Flow), 장면 흐름(Scene Flow), 관성 적분(Inertial Integration), 운동 추정(Motion Estimation)은 연속적인 측정값을 지속적으로 변화하는 환경 해석으로 변환한다.

다중 센서 인지(Multi-Sensor Perception)는 서로 보완적인 물리적 관측을 결합하여 환경에 대한 해석을 향상시킨다. 카메라는 풍부한 외형과 의미 정보를 제공하지만 조명과 가시성에 크게 영향을 받는다. 라이다는 직접적인 기하학적 거리 정보를 제공하고, 레이더는 강인한 거리와 방사 속도(Radial Velocity) 정보를 제공하며, IMU는 높은 주기의 운동 정보를 제공한다. 이러한 센서의 결합은 개별 센서의 약점을 줄일 수 있지만, 센서 융합(Sensor Fusion)을 위해서는 정밀한 공간 보정, 시간 동기화, 불확실성 처리 및 고장 검출(Failure Detection)이 필요하다.

따라서 완전한 인지 파이프라인(Perception Pipeline)을 원시 데이터에서 레이블(Label)로 변환되는 단순한 단방향 과정으로 이해해서는 안 된다. 정보는 원시 측정값과 기하학적 기본 요소(Geometric Primitive)에서 의미론적 객체와 장면 수준 관계(Scene-Level Relationship)에 이르기까지 여러 추상화 수준에서 동시에 존재할 수 있다. 후속 모듈은 서로 다른 수준의 정보를 직접 사용할 수 있으며, 장애물 회피는 점유 정보(Occupancy)를 사용하고, 조작은 객체 자세를 요구하며, 상위 수준의 피지컬 AI(Physical AI)는 추론을 위해 의미론적 표현 또는 학습된 표현을 사용할 수 있다.

가능한 경우 불확실성(Uncertainty)도 이러한 변환 과정과 함께 전달되어야 한다. 센서 잡음, 불완전한 관측, 모호한 분류, 가림, 보정 오차 및 도메인 변화(Domain Change)로 인해 인지 결과가 완벽하게 확실할 수는 없다. 신뢰도 점수(Confidence Score), 공분산 추정(Covariance Estimation), 확률적 점유(Probabilistic Occupancy), 다중 가설(Multiple Hypothesis), 일관성 검사(Consistency Check)를 사용하면 후속 시스템이 강한 증거와 약한 증거를 구분할 수 있다. 이러한 구분은 인지가 안전 중요 로봇 동작(Safety-Critical Robot Behavior)에 직접 관여할 때 특히 중요하다.

실시간 동작(Real-Time Operation)은 또 다른 핵심 제약 조건을 제공한다. 인지 시스템은 정보가 쓸모없어지기 전에 충분히 정확한 결과를 생성해야 한다. 고해상도 센싱과 대규모 신경망 모델은 표현의 풍부함을 향상시킬 수 있지만 계산량, 메모리 트래픽(Memory Traffic), 지연시간(Latency)도 증가시킨다. 따라서 실제 로봇은 인지 정확도만 독립적으로 최적화하는 것이 아니라 센싱 주기, 표현 복잡도, 모델 정확도, 하드웨어 성능 및 종단 간 지연시간(End-to-End Latency)의 균형을 맞춰야 한다.

감지--표현--이해(Sense--Represent--Understand) 추상화는 전통적 로보틱스(Classical Robotics)와 현대 인공지능(Modern AI)의 관계를 이해하는 데에도 유용하다. 전통적인 시스템은 명시적인 기하학 표현을 구성한 후 정의된 추정 및 인식 알고리즘을 적용하는 경우가 많다. 학습 기반 시스템(Learning-Based System)은 센서 측정값을 직접 잠재 특징(Latent Feature)과 의미론적 예측으로 변환할 수 있다. 종단간 접근법(End-to-End Approach)은 단계 사이의 명시적인 경계를 더욱 줄일 수 있지만, 감지, 내부 표현, 해석이라는 개념적 기능은 여전히 계산 과정 내부에 존재한다.

피지컬 AI(Physical AI) 시스템에서 인지의 궁극적인 목적은 단순한 관측이 아니라 행동(Action)을 지원하는 것이다. 유용한 인지 표현은 로봇이 어디로 이동할 수 있는지, 무엇을 조작할 수 있는지, 어떤 객체가 작업에 중요한지, 환경이 어떻게 변화하고 있는지, 그리고 어느 정도의 불확실성이 남아 있는지를 판단할 수 있도록 해야 한다. 따라서 인지는 3차원 장면 이해(3D Scene Understanding), 점유 및 의미 지도(Occupancy and Semantic Maps), 피지컬 AI 인지(Perception for Physical AI), 촉각 감지(Tactile Perception), 개방형 어휘 인지(Open-Vocabulary Perception)와 같은 보다 높은 수준의 기능으로 확장된다.

전체 파이프라인은 결국 물리적 증거(Physical Evidence)를 단계적으로 변환하는 과정으로 이해할 수 있다. 감지(Sense)는 현실 세계에서 측정값을 획득하고, 표현(Represent)은 그 측정값을 계산 가능한 구조로 조직하며, 이해(Understand)는 로봇의 목표와 관련된 의미를 추출한다. 신뢰할 수 있는 자율 행동은 이러한 단계들이 기하학적으로 일관되고, 시간적으로 동기화되며, 불확실성을 고려하고, 계산 효율성을 유지하면서 이후의 판단과 행동에 긴밀하게 연결될 때 가능해진다.

##  

## 01.02. Perception System Architecture Modular vs End to End

![](images/image2.png){width="7.268055555555556in" height="7.268055555555556in"}

Robot perception systems can be organized along a spectrum ranging from strongly modular architectures to highly integrated end-to-end architectures. A modular system divides perception into explicit processing stages with defined interfaces, while an end-to-end system learns larger portions of the transformation from sensor observations to task-relevant outputs. The architectural choice determines how information, uncertainty, computation, and responsibility are distributed across the robot software stack.

A conventional modular perception architecture decomposes the system into components such as sensor preprocessing, calibration, feature extraction, object detection, segmentation, tracking, sensor fusion, scene representation, and downstream decision interfaces. Each component receives a defined input and produces an interpretable output. This structure follows naturally from the Sense--Represent--Understand pipeline and allows individual perception functions to be developed and validated separately.

For example, camera images may first pass through calibration and preprocessing before entering an object detector. LiDAR measurements can independently undergo filtering, voxelization, ground removal, and three-dimensional detection. Radar may estimate range and velocity, while IMU processing estimates motion. A fusion module subsequently combines selected outputs into tracks, occupancy information, semantic objects, or another representation consumed by navigation and planning.

The principal advantage of modular architecture is separation of concerns. Developers can select algorithms appropriate to individual sensor modalities and replace one component without redesigning the entire perception system. A new detector can replace an older detector while preserving the tracking interface, or a LiDAR model can be upgraded without changing camera preprocessing. Stable interfaces therefore support incremental engineering and long product life cycles.

Modularity also improves observability and debugging. When perception fails, engineers can inspect intermediate outputs such as calibrated images, filtered point clouds, detections, tracks, transformation matrices, confidence values, and occupancy maps. This makes it possible to localize whether an error originated in sensing, calibration, preprocessing, inference, fusion, or interpretation. Such traceability is particularly valuable during field testing and production failure analysis.

Another important benefit is explicit management of uncertainty and failure. Individual modules can expose confidence scores, covariance matrices, validity flags, diagnostic states, and timing information. A fusion component can reject inconsistent measurements or reduce the influence of a degraded sensor. The system can also continue operating in a reduced capability mode when one perception component becomes unavailable, provided that sufficient redundancy exists elsewhere in the architecture.

However, modular systems can lose information at component boundaries. A detector may compress a rich image feature map into bounding boxes before fusion, discarding appearance information that could have helped another sensor modality. Hand-designed interfaces may therefore create information bottlenecks. Errors also propagate sequentially: inaccurate calibration affects detection geometry, detection errors affect tracking, and tracking errors can ultimately influence planning and control.

End-to-end perception attempts to reduce these manually defined boundaries by learning a larger mapping jointly. Instead of independently producing many intermediate outputs, a neural architecture may consume camera images, LiDAR points, radar features, temporal observations, or combinations of them and directly generate task-oriented representations. These outputs can include objects, occupancy, trajectories, semantic features, or latent representations used by subsequent learned components.

Joint optimization is one of the strongest motivations for end-to-end design. When multiple processing stages are differentiable, training can optimize internal representations according to a common objective rather than optimizing each module independently. Features useful for the final task can emerge automatically, and information that would have been removed by handcrafted intermediate interfaces can remain available throughout the network.

Deep sensor fusion illustrates this advantage. Instead of separately completing camera detection and LiDAR detection before combining their final results, an integrated network can transform measurements from both sensors into a shared feature space. Camera appearance can complement LiDAR geometry before object-level decisions are made. Similar approaches can aggregate temporal observations and bird\'s-eye-view features into unified representations for scene understanding.

The benefits of integration introduce new engineering difficulties. Internal neural representations are usually less interpretable than explicit geometric or semantic interfaces, making failures harder to isolate. A wrong output may result from interactions among training data, feature extraction, attention, fusion, temporal context, or optimization rather than from a clearly identifiable module. Validation must therefore examine not only output accuracy but also robustness across operating conditions and failure scenarios.

End-to-end systems also depend heavily on training data. A modular geometric algorithm may retain predictable behavior across environments when its physical assumptions remain valid, whereas a learned system can behave unexpectedly outside its training distribution. Changes in cameras, lenses, LiDAR configurations, mounting positions, weather, illumination, environments, or object populations may require additional data collection, validation, adaptation, or retraining.

The distinction between modular and end-to-end architecture should therefore not be treated as an absolute binary choice. Modern robot perception commonly adopts hybrid architectures. Low-level sensor drivers, timestamping, calibration, coordinate transformations, health monitoring, and safety diagnostics often remain explicit modules, while computationally intensive interpretation may use deeply integrated neural networks. Learned outputs can then feed conventional tracking, mapping, planning, and safety components.

Hybrid architecture can also preserve multiple representations simultaneously. A neural perception network may produce semantic features and object hypotheses while a geometric pipeline independently maintains occupancy or free-space information. The robot can exploit learned semantic capability without abandoning physically interpretable representations. Redundant pathways can additionally provide consistency checks when learned and geometric estimates disagree.

The appropriate architectural boundary depends strongly on the robot platform and mission. Industrial AMRs often benefit from modularity because maintainability, deterministic interfaces, diagnostics, and long-term deployment are important. Manipulation systems may integrate vision and learned features more tightly because object geometry and semantics directly influence grasping. Physical AI systems can require even richer shared representations that connect perception with language, world models, reasoning, and action.

Real-time constraints further influence this choice. Modular pipelines allow components to operate at different frequencies: an IMU may update rapidly, LiDAR processing at a moderate rate, and computationally expensive semantic inference at a lower rate. End-to-end models can reduce duplicated processing but may create a large inference block whose latency and memory consumption become difficult to partition. Deployment architecture must therefore consider scheduling, accelerators, memory bandwidth, and worst-case latency.

Safety requirements generally favor clearly defined fallback behavior regardless of the selected architecture. A highly integrated learned model should not imply that every safety function must also become end-to-end. Independent obstacle detection, sensor-health monitoring, geometric constraints, confidence thresholds, degraded modes, and emergency stopping mechanisms can remain outside the learned perception path. This creates architectural diversity between performance-oriented intelligence and safety-oriented protection.

Modular systems are consequently strongest when transparency, replaceability, verification, heterogeneous sensors, and controlled failure handling dominate the design requirements. End-to-end systems become attractive when rich multimodal information must be preserved, large datasets are available, and joint optimization can significantly improve task performance. Hybrid systems attempt to combine these strengths by defining explicit boundaries only where they provide engineering, operational, or safety value.

The long-term direction of robot perception is therefore better described as selective integration rather than the disappearance of modularity. Learned models will increasingly connect sensing, spatial representation, semantics, temporal reasoning, and action-related features, while explicit interfaces will remain important for hardware abstraction, synchronization, diagnostics, safety, and system integration. Architecture should be chosen according to what information must remain interpretable and what information benefits from joint learning.

A production perception system should ultimately be evaluated as a complete robot capability rather than by architectural ideology. The correct design is the one that delivers sufficient accuracy, latency, robustness, diagnosability, maintainability, and safety for the intended operating environment. Modular, end-to-end, and hybrid architectures are therefore engineering alternatives within the same objective: converting uncertain sensor observations into reliable information that enables autonomous physical action.

로봇 인지 시스템(Robot Perception System)은 강한 모듈형 아키텍처(Modular Architecture)에서 고도로 통합된 종단간 아키텍처(End-to-End Architecture)에 이르는 연속적인 범위에서 구성될 수 있다. 모듈형 시스템은 인지를 명확한 인터페이스(Interface)를 가진 처리 단계로 분리하는 반면, 종단간 시스템은 센서 관측에서 작업 관련 출력까지의 변환 과정 중 더 많은 부분을 학습한다. 이러한 아키텍처 선택은 정보, 불확실성, 계산 자원 및 기능적 책임이 로봇 소프트웨어 스택(Robot Software Stack)에 어떻게 분배되는지를 결정한다.

전통적인 모듈형 인지 아키텍처(Modular Perception Architecture)는 시스템을 센서 전처리(Sensor Preprocessing), 보정(Calibration), 특징 추출(Feature Extraction), 객체 검출(Object Detection), 분할(Segmentation), 추적(Tracking), 센서 융합(Sensor Fusion), 장면 표현(Scene Representation), 후속 의사결정 인터페이스(Downstream Decision Interface) 등의 구성요소로 분해한다. 각 구성요소는 정의된 입력을 받아 해석 가능한 출력을 생성한다. 이러한 구조는 감지--표현--이해(Sense--Represent--Understand) 파이프라인에서 자연스럽게 이어지며, 개별 인지 기능을 독립적으로 개발하고 검증할 수 있도록 한다.

예를 들어 카메라 영상(Camera Image)은 객체 검출기(Object Detector)에 입력되기 전에 보정과 전처리를 수행할 수 있다. 라이다(LiDAR) 측정값은 독립적으로 필터링(Filtering), 복셀화(Voxelization), 지면 제거(Ground Removal), 3차원 객체 검출(3D Detection)을 수행할 수 있다. 레이더(Radar)는 거리와 속도를 추정하고, 관성측정장치(IMU) 처리는 운동 상태를 추정한다. 이후 융합 모듈(Fusion Module)은 선택된 출력들을 결합하여 추적 객체(Track), 점유 정보(Occupancy Information), 의미론적 객체(Semantic Object) 또는 내비게이션과 경로 계획에서 사용하는 다른 표현을 생성한다.

모듈형 아키텍처의 주요 장점은 관심사의 분리(Separation of Concerns)이다. 개발자는 개별 센서 모달리티(Sensor Modality)에 적합한 알고리즘을 선택할 수 있으며, 전체 인지 시스템을 다시 설계하지 않고 하나의 구성요소만 교체할 수 있다. 기존 추적 인터페이스를 유지하면서 새로운 검출기로 교체하거나, 카메라 전처리를 변경하지 않고 라이다 모델을 업그레이드할 수 있다. 따라서 안정적인 인터페이스는 점진적인 엔지니어링(Incremental Engineering)과 긴 제품 수명주기(Product Life Cycle)를 지원한다.

모듈성(Modularity)은 관측 가능성(Observability)과 디버깅(Debugging)도 향상시킨다. 인지에 문제가 발생하면 엔지니어는 보정된 영상, 필터링된 포인트 클라우드(Point Cloud), 검출 결과, 추적 결과, 변환 행렬(Transformation Matrix), 신뢰도 값(Confidence Value), 점유 지도(Occupancy Map) 등의 중간 출력을 검사할 수 있다. 이를 통해 오류가 센싱, 보정, 전처리, 추론(Inference), 융합 또는 해석 중 어디에서 발생했는지를 파악할 수 있다. 이러한 추적 가능성(Traceability)은 현장 시험(Field Testing)과 양산 시스템의 고장 분석(Production Failure Analysis)에서 특히 중요하다.

또 다른 중요한 장점은 불확실성(Uncertainty)과 고장(Failure)을 명시적으로 관리할 수 있다는 것이다. 개별 모듈은 신뢰도 점수(Confidence Score), 공분산 행렬(Covariance Matrix), 유효성 플래그(Validity Flag), 진단 상태(Diagnostic State), 시간 정보를 외부로 제공할 수 있다. 융합 구성요소는 서로 일치하지 않는 측정값을 제거하거나 성능이 저하된 센서의 영향력을 감소시킬 수 있다. 또한 충분한 중복성(Redundancy)이 존재한다면 하나의 인지 구성요소를 사용할 수 없게 되더라도 시스템은 성능 저하 모드(Degraded Capability Mode)로 계속 동작할 수 있다.

그러나 모듈형 시스템은 구성요소의 경계에서 정보를 잃을 수 있다. 객체 검출기가 풍부한 이미지 특징 맵(Feature Map)을 경계 상자(Bounding Box)로 압축한 후 융합한다면, 다른 센서 모달리티에 유용할 수 있었던 외형 정보가 사라질 수 있다. 따라서 사람이 설계한 인터페이스는 정보 병목(Information Bottleneck)을 만들 수 있다. 또한 오차는 순차적으로 전파된다. 부정확한 보정은 검출 기하학에 영향을 주고, 검출 오류는 추적에 영향을 주며, 추적 오류는 최종적으로 경로 계획과 제어에 영향을 미칠 수 있다.

종단간 인지(End-to-End Perception)는 더 큰 범위의 변환 과정을 공동으로 학습함으로써 이러한 인위적으로 정의된 경계를 줄이고자 한다. 여러 개의 중간 출력을 독립적으로 생성하는 대신, 하나의 신경망 아키텍처(Neural Architecture)가 카메라 영상, 라이다 포인트, 레이더 특징, 시간적 관측 또는 이들의 조합을 입력받아 작업 중심 표현(Task-Oriented Representation)을 직접 생성할 수 있다. 출력은 객체, 점유 정보, 궤적(Trajectory), 의미론적 특징 또는 이후의 학습 기반 구성요소에서 사용하는 잠재 표현(Latent Representation)이 될 수 있다.

공동 최적화(Joint Optimization)는 종단간 설계를 사용하는 가장 강력한 이유 중 하나이다. 여러 처리 단계가 미분 가능(Differentiable)하다면 학습 과정은 각 모듈을 독립적으로 최적화하는 대신 공통 목적 함수(Common Objective)를 기준으로 내부 표현을 최적화할 수 있다. 최종 작업에 유용한 특징이 자동으로 형성될 수 있으며, 수작업으로 정의된 중간 인터페이스에서 제거되었을 정보도 네트워크 전체에 걸쳐 유지될 수 있다.

심층 센서 융합(Deep Sensor Fusion)은 이러한 장점을 잘 보여준다. 카메라 검출과 라이다 검출을 각각 완료한 후 최종 결과를 결합하는 대신, 통합 신경망은 두 센서의 측정값을 공통 특징 공간(Shared Feature Space)으로 변환할 수 있다. 카메라의 외형 정보는 객체 수준의 판단이 이루어지기 전에 라이다의 기하학 정보를 보완할 수 있다. 이와 유사한 방식으로 시간적 관측과 조감도 특징(Bird\'s-Eye-View Feature)을 통합된 표현으로 집계하여 장면 이해(Scene Understanding)에 활용할 수 있다.

이러한 통합의 장점은 새로운 엔지니어링 문제를 동반한다. 내부 신경망 표현은 일반적으로 명시적인 기하학 또는 의미론적 인터페이스보다 해석 가능성(Interpretability)이 낮기 때문에 고장의 원인을 분리하기가 어렵다. 잘못된 출력은 명확하게 식별 가능한 하나의 모듈이 아니라 학습 데이터, 특징 추출, 어텐션(Attention), 융합, 시간적 문맥(Temporal Context), 최적화 과정의 상호작용으로 발생할 수 있다. 따라서 검증(Validation)은 출력 정확도뿐 아니라 다양한 운용 조건과 고장 상황에서의 강건성(Robustness)도 평가해야 한다.

종단간 시스템은 학습 데이터(Training Data)에 대한 의존성도 높다. 모듈형 기하학 알고리즘은 물리적 가정이 유지되는 환경에서는 비교적 예측 가능한 동작을 유지할 수 있지만, 학습 기반 시스템은 학습 분포(Training Distribution)를 벗어난 상황에서 예상하지 못한 동작을 보일 수 있다. 카메라, 렌즈, 라이다 구성, 장착 위치, 날씨, 조명, 환경 또는 객체 종류가 변경되면 추가적인 데이터 수집, 검증, 적응(Adaptation) 또는 재학습(Retraining)이 필요할 수 있다.

따라서 모듈형 아키텍처와 종단간 아키텍처의 차이를 절대적인 이분법(Binary Choice)으로 이해해서는 안 된다. 현대의 로봇 인지 시스템은 일반적으로 하이브리드 아키텍처(Hybrid Architecture)를 채택한다. 저수준 센서 드라이버, 타임스탬프 처리, 보정, 좌표 변환, 상태 모니터링(Health Monitoring), 안전 진단(Safety Diagnostics)은 명시적인 모듈로 유지하면서 계산량이 많은 해석 과정에는 깊게 통합된 신경망을 사용할 수 있다. 이후 학습된 출력은 전통적인 추적, 매핑, 경로 계획 및 안전 구성요소로 전달될 수 있다.

하이브리드 아키텍처는 여러 종류의 표현을 동시에 유지할 수도 있다. 신경망 기반 인지 네트워크가 의미론적 특징과 객체 가설(Object Hypothesis)을 생성하는 동안, 기하학 파이프라인(Geometric Pipeline)은 독립적으로 점유 정보 또는 자유 공간(Free Space)을 유지할 수 있다. 이를 통해 로봇은 물리적으로 해석 가능한 표현을 포기하지 않으면서 학습 기반 의미론적 능력을 활용할 수 있다. 또한 학습 기반 추정과 기하학적 추정이 서로 일치하지 않을 때 중복된 경로를 이용하여 일관성 검사(Consistency Check)를 수행할 수 있다.

적절한 아키텍처 경계(Architectural Boundary)는 로봇 플랫폼과 임무에 크게 의존한다. 산업용 자율이동로봇(AMR)은 유지보수성(Maintainability), 결정론적 인터페이스(Deterministic Interface), 진단 가능성(Diagnosability), 장기간 운용이 중요하기 때문에 모듈성이 유리한 경우가 많다. 조작 시스템(Manipulation System)은 객체의 기하학과 의미가 파지에 직접 영향을 주므로 비전과 학습된 특징을 더욱 긴밀하게 통합할 수 있다. 피지컬 AI(Physical AI) 시스템은 인지를 언어, 월드 모델(World Model), 추론(Reasoning), 행동과 연결하는 더욱 풍부한 공유 표현(Shared Representation)을 요구할 수 있다.

실시간 제약(Real-Time Constraint) 역시 아키텍처 선택에 영향을 준다. 모듈형 파이프라인은 각 구성요소를 서로 다른 주기로 동작시킬 수 있다. IMU는 매우 높은 주기로 갱신하고, 라이다 처리는 중간 수준의 주기로 수행하며, 계산량이 큰 의미론적 추론은 더 낮은 주기로 실행할 수 있다. 종단간 모델은 중복 계산을 줄일 수 있지만 하나의 대규모 추론 블록(Inference Block)을 형성하여 지연시간과 메모리 사용량을 분할하기 어렵게 만들 수 있다. 따라서 배포 아키텍처(Deployment Architecture)는 스케줄링, 가속기(Accelerator), 메모리 대역폭(Memory Bandwidth), 최악 조건 지연시간(Worst-Case Latency)을 함께 고려해야 한다.

안전 요구사항(Safety Requirement)은 선택한 아키텍처와 관계없이 명확하게 정의된 대체 동작(Fallback Behavior)을 요구한다. 고도로 통합된 학습 모델을 사용한다고 해서 모든 안전 기능까지 종단간 방식으로 구성해야 하는 것은 아니다. 독립적인 장애물 검출, 센서 상태 모니터링, 기하학적 제약(Geometric Constraint), 신뢰도 임계값(Confidence Threshold), 성능 저하 모드, 비상 정지(Emergency Stop) 기능은 학습 기반 인지 경로 외부에 유지할 수 있다. 이를 통해 성능 중심의 지능과 안전 중심의 보호 기능 사이에 아키텍처적 다양성(Architectural Diversity)을 확보할 수 있다.

결과적으로 모듈형 시스템은 투명성(Transparency), 교체 가능성(Replaceability), 검증 가능성(Verifiability), 이종 센서(Heterogeneous Sensor), 통제된 고장 처리가 주요 설계 요구사항일 때 강점을 가진다. 종단간 시스템은 풍부한 다중모달 정보(Multimodal Information)를 유지해야 하고 대규모 데이터셋을 사용할 수 있으며 공동 최적화를 통해 작업 성능을 크게 향상시킬 수 있을 때 매력적이다. 하이브리드 시스템은 엔지니어링, 운용 또는 안전 측면에서 명시적인 경계가 필요한 위치에만 인터페이스를 정의함으로써 두 접근법의 장점을 결합하려 한다.

따라서 로봇 인지의 장기적인 발전 방향은 모듈성의 소멸보다는 선택적 통합(Selective Integration)으로 설명하는 것이 더 적절하다. 학습 모델은 센싱, 공간 표현, 의미론, 시간적 추론 및 행동 관련 특징을 점점 더 긴밀하게 연결하게 될 것이지만, 명시적인 인터페이스는 하드웨어 추상화(Hardware Abstraction), 동기화, 진단, 안전 및 시스템 통합에서 계속 중요한 역할을 수행할 것이다. 어떤 정보를 해석 가능한 형태로 유지해야 하는지, 어떤 정보가 공동 학습의 이점을 얻을 수 있는지를 기준으로 아키텍처를 선택해야 한다.

궁극적으로 양산용 인지 시스템(Production Perception System)은 특정 아키텍처에 대한 선호가 아니라 완전한 로봇 기능(Complete Robot Capability)의 관점에서 평가해야 한다. 올바른 설계는 목표 운용 환경에서 필요한 정확도, 지연시간, 강건성, 진단 가능성, 유지보수성 및 안전성을 제공하는 설계이다. 따라서 모듈형, 종단간, 하이브리드 아키텍처는 동일한 목표를 달성하기 위한 서로 다른 엔지니어링 대안이며, 그 목표는 불확실한 센서 관측을 신뢰할 수 있는 정보로 변환하여 자율적인 물리적 행동(Autonomous Physical Action)을 가능하게 하는 것이다.

##  

## 01.03. Spatial Representation Voxels Point Clouds Meshes

![](images/image3.png){width="7.268055555555556in" height="7.268055555555556in"}

Spatial representation is the computational process of describing the geometry and organization of the physical environment in a form that a robot can store, analyze, and use for action. Raw sensor measurements alone do not provide a complete spatial model. Perception systems therefore transform observations into structured representations such as point clouds, voxels, and meshes, each preserving different properties of three-dimensional space.

The choice of spatial representation affects nearly every downstream perception function. Object detection, collision checking, localization, mapping, terrain analysis, manipulation, and scene understanding require different combinations of geometric precision, computational efficiency, memory consumption, and update speed. A representation suitable for detailed object reconstruction may therefore be inefficient for real-time navigation, while a compact navigation map may discard geometry needed for manipulation.

Point clouds represent three-dimensional environments as collections of discrete points with coordinates such as x, y, and z. Each point corresponds to a sampled location on a visible surface or detected structure. Additional attributes may include intensity, color, timestamp, surface normal, semantic class, or sensor confidence. LiDAR naturally produces point-cloud-like measurements, while stereo cameras and depth cameras can also generate point clouds from estimated depth.

The major advantage of a point cloud is that it preserves measured geometry without requiring space to be completely discretized. Large empty regions consume little or no storage because only observed points need to be represented. This makes point clouds particularly effective for LiDAR perception, three-dimensional object detection, registration, localization, mapping, and environmental reconstruction where accurate measured coordinates are important.

However, point clouds are irregular and unordered data structures. Neighboring points are not automatically connected, point density changes with distance and viewing angle, and different sensors can produce substantially different sampling patterns. Algorithms must therefore construct neighborhoods, spatial indexes, pillars, range images, or learned point features before higher-level reasoning can be performed efficiently. Sparse measurements can also leave large portions of surfaces unobserved.

Voxel representation addresses some of these difficulties by dividing three-dimensional space into discrete volumetric cells called voxels. A voxel can store occupancy, density, probability, semantic category, learned features, or other properties associated with a small region of space. Conceptually, voxels extend the two-dimensional pixel grid into three dimensions and provide a regular spatial organization suitable for structured computation.

Regular voxel grids simplify neighborhood operations because adjacent spatial regions have predictable relationships. Three-dimensional convolution, occupancy reasoning, collision checking, semantic mapping, and volumetric fusion can therefore operate naturally on voxelized data. Point clouds are frequently converted into voxels before neural-network inference because voxelization transforms irregular measurements into a representation that can be processed more efficiently by structured or sparse convolutional architectures.

The primary limitation of dense voxel grids is memory growth. Halving the voxel dimension along each spatial axis can increase the number of cells dramatically because resolution expands simultaneously in three dimensions. A large environment represented at fine resolution therefore becomes computationally expensive. This creates a fundamental trade-off between spatial precision and resource consumption, particularly for robots performing real-time perception on edge computing hardware.

Sparse voxel structures reduce this problem by storing only occupied or relevant cells instead of allocating memory to the entire three-dimensional volume. Sparse tensors, hierarchical grids, octrees, and related structures exploit the fact that most physical environments contain substantial empty space. These approaches enable higher spatial resolution over larger regions while retaining many of the computational advantages associated with voxel-based representations.

Voxel size itself becomes an important architectural parameter. Large voxels reduce memory consumption and accelerate computation but merge nearby geometric details. Small voxels preserve fine structures but increase processing cost and sensitivity to measurement noise. A perception system may therefore use different resolutions for different purposes, such as coarse occupancy for navigation and finer local geometry around obstacles or manipulation targets.

Meshes represent surfaces explicitly using connected geometric primitives, most commonly vertices, edges, and triangular faces. Unlike point clouds, which contain isolated samples, meshes encode relationships between neighboring surface locations. This connectivity allows a robot to represent continuous surfaces, object boundaries, slopes, terrain, and reconstructed structures more directly than an unconnected set of measured points.

A mesh can provide a compact and visually coherent representation of surfaces when the underlying geometry is sufficiently known. Surface normals, curvature, texture coordinates, material properties, and semantic labels can be associated with mesh elements. These characteristics make meshes valuable for three-dimensional reconstruction, digital twins, manipulation planning, simulation, inspection, terrain modeling, and applications where surface topology is important.

Generating a reliable mesh from sensor observations is nevertheless more complex than storing raw points. Measurements contain noise, occlusion, missing regions, moving objects, and inconsistent sampling density. Surface reconstruction must infer connectivity between observations and determine which measurements belong to the same physical surface. Incorrect assumptions can produce holes, artificial connections, distorted surfaces, or geometry that does not correspond to the actual environment.

Point clouds, voxels, and meshes should therefore be understood as complementary rather than competing representations. A perception pipeline may initially receive a LiDAR point cloud, transform it into sparse voxels for neural inference, maintain probabilistic voxel occupancy for navigation, and construct a mesh from accumulated observations for visualization or detailed reconstruction. Information can move between representations according to the requirements of each processing stage.

Coordinate frames provide the geometric foundation that allows these representations to remain consistent. Points, voxel centers, and mesh vertices must be expressed relative to defined sensor, robot, odometry, or map frames. When a robot moves, transformations derived from localization and calibration allow observations collected at different times and from different sensors to be accumulated into a common spatial reference.

Temporal updates introduce additional complexity because real environments are not static. A representation built from previous observations can become incorrect when people, vehicles, doors, equipment, or other objects move. Perception systems must distinguish persistent geometry from dynamic observations and decide when spatial information should be updated, decayed, replaced, or removed. This is particularly important for long-running autonomous robots operating in shared environments.

Spatial representations can also carry uncertainty rather than treating geometry as perfectly known. A point may contain measurement confidence, a voxel may store occupancy probability, and reconstructed surfaces may reflect confidence derived from repeated observations. Maintaining uncertainty allows the robot to distinguish confirmed free space from unobserved space and prevents incomplete sensor measurements from being interpreted as definitive geometric knowledge.

Semantic information further extends purely geometric representations. Points can receive class labels, voxels can contain semantic probabilities or learned features, and mesh surfaces can be associated with objects, materials, or functional regions. Geometry then answers where something exists, while semantics describes what it is or how it may be used. This combination forms an important foundation for higher-level scene understanding and Physical AI.

Robot platforms emphasize different representations according to their physical tasks. An outdoor AMR may rely heavily on point clouds and occupancy structures for obstacle detection and traversability analysis. A quadruped may require dense local terrain geometry for foothold selection. A manipulator may need detailed object surfaces and pose information, while a UAV may favor sparse representations that cover large three-dimensional regions with limited computational resources.

Modern perception architectures increasingly maintain several spatial representations simultaneously instead of selecting only one. Raw or filtered point clouds preserve sensor evidence, sparse voxels support efficient neural inference, occupancy structures support navigation, and meshes or reconstructed surfaces support detailed geometric reasoning. Learned feature volumes can coexist with all of these structures, providing task-oriented information that is difficult to describe using geometry alone.

The correct spatial representation is therefore determined by the robot\'s action requirements, sensor configuration, operating scale, latency budget, and available computing resources. Point clouds provide flexible sampled geometry, voxels provide structured volumetric reasoning, and meshes provide connected surface models. Production systems frequently combine them so that each representation contributes where its geometric and computational properties are most useful.

Spatial representation ultimately forms the bridge between sensing and physical reasoning. Sensors observe fragments of the environment, but autonomous robots require organized spatial knowledge to determine free space, obstacles, surfaces, objects, and possible interactions. By selecting and combining point clouds, voxels, meshes, and related structures appropriately, perception systems transform fragmented measurements into spatial models that can support reliable autonomous action.

공간 표현(Spatial Representation)은 물리적 환경의 기하학적 형상과 공간 구성을 로봇이 저장하고 분석하며 행동에 활용할 수 있는 형태로 기술하는 계산 과정이다. 원시 센서 측정값(Raw Sensor Measurement)만으로는 완전한 공간 모델을 제공할 수 없다. 따라서 인지 시스템(Perception System)은 관측 정보를 포인트 클라우드(Point Cloud), 복셀(Voxel), 메시(Mesh)와 같은 구조화된 표현으로 변환하며, 각각은 3차원 공간의 서로 다른 특성을 보존한다.

공간 표현의 선택은 거의 모든 후속 인지 기능(Downstream Perception Function)에 영향을 미친다. 객체 검출(Object Detection), 충돌 검사(Collision Checking), 위치추정(Localization), 매핑(Mapping), 지형 분석(Terrain Analysis), 조작(Manipulation), 장면 이해(Scene Understanding)는 서로 다른 수준의 기하학적 정밀도, 계산 효율성, 메모리 사용량 및 갱신 속도를 요구한다. 따라서 정밀한 객체 재구성에 적합한 표현이 실시간 내비게이션에는 비효율적일 수 있으며, 압축된 내비게이션 지도는 조작에 필요한 기하학적 세부 정보를 제거할 수 있다.

포인트 클라우드(Point Cloud)는 3차원 환경을 x, y, z와 같은 좌표를 가진 이산적인 점(Discrete Point)의 집합으로 표현한다. 각 점은 관측 가능한 표면이나 검출된 구조에서 샘플링된 위치에 대응한다. 추가적인 속성으로 반사 강도(Intensity), 색상(Color), 타임스탬프(Timestamp), 표면 법선(Surface Normal), 의미론적 클래스(Semantic Class), 센서 신뢰도(Sensor Confidence) 등을 포함할 수 있다. 라이다(LiDAR)는 본질적으로 포인트 클라우드 형태의 측정값을 생성하며, 스테레오 카메라(Stereo Camera)와 깊이 카메라(Depth Camera) 역시 추정된 깊이로부터 포인트 클라우드를 생성할 수 있다.

포인트 클라우드의 주요 장점은 전체 공간을 완전히 이산화하지 않고도 측정된 기하학적 정보를 보존할 수 있다는 점이다. 관측된 점만 표현하면 되므로 넓은 빈 공간(Empty Space)은 거의 또는 전혀 저장 공간을 소비하지 않는다. 이러한 특성으로 인해 포인트 클라우드는 정확한 측정 좌표가 중요한 라이다 인지(LiDAR Perception), 3차원 객체 검출, 정합(Registration), 위치추정, 매핑 및 환경 재구성(Environmental Reconstruction)에 특히 효과적이다.

그러나 포인트 클라우드는 불규칙하고 순서가 없는 데이터 구조(Irregular and Unordered Data Structure)이다. 인접한 점들이 자동으로 연결되지 않으며, 점의 밀도(Point Density)는 거리와 관측 각도에 따라 달라지고, 센서 종류에 따라서도 샘플링 패턴이 크게 달라질 수 있다. 따라서 알고리즘이 효율적으로 상위 수준의 추론을 수행하려면 이웃 관계(Neighborhood), 공간 인덱스(Spatial Index), 필러(Pillar), 거리 영상(Range Image) 또는 학습된 포인트 특징(Learned Point Feature)을 구성해야 한다. 희소한 측정값은 표면의 많은 영역을 관측되지 않은 상태로 남길 수도 있다.

복셀 표현(Voxel Representation)은 3차원 공간을 복셀이라 불리는 이산적인 체적 셀(Volumetric Cell)로 분할함으로써 이러한 문제 중 일부를 해결한다. 하나의 복셀에는 공간의 작은 영역과 관련된 점유 상태(Occupancy), 밀도(Density), 확률(Probability), 의미론적 범주(Semantic Category), 학습 특징(Learned Feature) 또는 기타 속성을 저장할 수 있다. 개념적으로 복셀은 2차원 픽셀 격자(Pixel Grid)를 3차원으로 확장한 것으로, 구조화된 계산에 적합한 규칙적인 공간 구성을 제공한다.

규칙적인 복셀 격자(Regular Voxel Grid)는 인접한 공간 영역 사이의 관계가 예측 가능하기 때문에 이웃 연산(Neighborhood Operation)을 단순화한다. 따라서 3차원 합성곱(3D Convolution), 점유 추론(Occupancy Reasoning), 충돌 검사, 의미론적 매핑(Semantic Mapping), 체적 융합(Volumetric Fusion)을 복셀화된 데이터에서 자연스럽게 수행할 수 있다. 포인트 클라우드는 신경망 추론(Neural Network Inference) 전에 복셀로 변환되는 경우가 많으며, 복셀화(Voxelization)는 불규칙한 측정값을 구조적 또는 희소 합성곱 아키텍처(Sparse Convolutional Architecture)가 효율적으로 처리할 수 있는 표현으로 변환한다.

밀집 복셀 격자(Dense Voxel Grid)의 가장 큰 한계는 메모리 사용량 증가이다. 각 공간 축에서 복셀의 크기를 절반으로 줄이면 세 차원 모두에서 동시에 해상도가 증가하기 때문에 전체 셀의 수가 급격하게 증가할 수 있다. 따라서 넓은 환경을 높은 해상도로 표현하면 계산 비용이 매우 커진다. 이는 특히 엣지 컴퓨팅 하드웨어(Edge Computing Hardware)에서 실시간 인지를 수행하는 로봇에서 공간 정밀도와 자원 소비 사이의 근본적인 절충 관계(Trade-Off)를 만든다.

희소 복셀 구조(Sparse Voxel Structure)는 전체 3차원 공간에 메모리를 할당하는 대신 점유되었거나 중요한 셀만 저장함으로써 이러한 문제를 줄인다. 희소 텐서(Sparse Tensor), 계층적 격자(Hierarchical Grid), 옥트리(Octree) 및 관련 구조는 대부분의 물리적 환경에 상당한 빈 공간이 존재한다는 특성을 활용한다. 이러한 접근법을 사용하면 복셀 기반 표현의 계산적 장점을 상당 부분 유지하면서 더 넓은 공간을 높은 해상도로 표현할 수 있다.

복셀 크기(Voxel Size) 자체도 중요한 아키텍처 파라미터(Architectural Parameter)가 된다. 큰 복셀은 메모리 사용량을 줄이고 계산을 가속하지만 서로 가까운 기하학적 세부 정보를 하나로 합칠 수 있다. 작은 복셀은 미세한 구조를 보존하지만 처리 비용이 증가하고 측정 잡음에 더욱 민감해진다. 따라서 인지 시스템은 내비게이션을 위한 거친 점유 표현과 장애물 또는 조작 대상 주변의 정밀한 국부 기하학처럼 목적에 따라 서로 다른 해상도를 사용할 수 있다.

메시(Mesh)는 연결된 기하학적 기본 요소(Geometric Primitive)를 사용하여 표면을 명시적으로 표현하며, 가장 일반적으로 정점(Vertex), 모서리(Edge), 삼각형 면(Triangular Face)을 사용한다. 서로 분리된 샘플로 구성되는 포인트 클라우드와 달리 메시는 인접한 표면 위치 사이의 연결 관계를 표현한다. 이러한 연결성을 통해 로봇은 연결되지 않은 측정 점들의 집합보다 연속적인 표면, 객체 경계, 경사면, 지형 및 재구성된 구조물을 더욱 직접적으로 표현할 수 있다.

기반 기하학 정보가 충분히 알려져 있다면 메시는 표면을 압축적이고 시각적으로 일관된 형태로 표현할 수 있다. 표면 법선(Surface Normal), 곡률(Curvature), 텍스처 좌표(Texture Coordinate), 재질 속성(Material Property), 의미론적 레이블(Semantic Label)을 메시 요소와 연결할 수 있다. 이러한 특성으로 인해 메시는 3차원 재구성(3D Reconstruction), 디지털 트윈(Digital Twin), 조작 계획(Manipulation Planning), 시뮬레이션(Simulation), 검사(Inspection), 지형 모델링(Terrain Modeling), 그리고 표면 위상(Surface Topology)이 중요한 응용 분야에서 유용하다.

그러나 센서 관측으로부터 신뢰할 수 있는 메시를 생성하는 과정은 원시 포인트를 저장하는 것보다 복잡하다. 측정값에는 잡음, 가림(Occlusion), 누락된 영역, 움직이는 객체, 불균일한 샘플링 밀도가 포함될 수 있다. 표면 재구성(Surface Reconstruction)은 관측값 사이의 연결성을 추론하고 어떤 측정값이 동일한 물리적 표면에 속하는지를 판단해야 한다. 잘못된 가정은 구멍(Hole), 인위적인 연결, 왜곡된 표면 또는 실제 환경과 일치하지 않는 기하학적 구조를 생성할 수 있다.

따라서 포인트 클라우드, 복셀, 메시는 서로 경쟁하는 표현이 아니라 상호 보완적인 표현(Complementary Representation)으로 이해해야 한다. 인지 파이프라인은 처음에 라이다 포인트 클라우드를 입력받아 신경망 추론을 위해 희소 복셀로 변환하고, 내비게이션을 위해 확률적 복셀 점유(Probabilistic Voxel Occupancy)를 유지하며, 시각화 또는 정밀한 재구성을 위해 누적된 관측으로부터 메시를 생성할 수 있다. 각 처리 단계의 요구사항에 따라 정보가 서로 다른 표현 사이에서 변환될 수 있다.

좌표계(Coordinate Frame)는 이러한 표현들의 일관성을 유지할 수 있게 하는 기하학적 기반을 제공한다. 점, 복셀 중심(Voxel Center), 메시 정점은 정의된 센서, 로봇, 오도메트리(Odometry) 또는 지도(Map) 좌표계를 기준으로 표현되어야 한다. 로봇이 이동할 때 위치추정과 보정에서 얻은 변환(Transformation)을 이용하면 서로 다른 시간과 센서에서 수집된 관측값을 공통된 공간 기준(Common Spatial Reference)에 누적할 수 있다.

실제 환경은 정적이지 않기 때문에 시간에 따른 갱신(Temporal Update)은 추가적인 복잡성을 만든다. 이전 관측으로 구축한 표현은 사람, 차량, 문, 장비 또는 다른 객체가 이동하면 더 이상 정확하지 않을 수 있다. 인지 시스템은 지속적인 기하학(Persistent Geometry)과 동적인 관측(Dynamic Observation)을 구분하고 공간 정보를 언제 갱신하고, 감쇠(Decay)시키고, 교체하거나 제거할지를 결정해야 한다. 이는 공유 환경에서 장시간 운용되는 자율 로봇에서 특히 중요하다.

공간 표현은 기하학적 정보를 완벽하게 알려진 값으로 취급하는 대신 불확실성(Uncertainty)을 함께 포함할 수도 있다. 하나의 점은 측정 신뢰도를 포함할 수 있고, 복셀은 점유 확률(Occupancy Probability)을 저장할 수 있으며, 재구성된 표면은 반복된 관측으로부터 계산된 신뢰도를 반영할 수 있다. 불확실성을 유지하면 로봇은 확인된 자유 공간과 아직 관측되지 않은 공간을 구분할 수 있으며, 불완전한 센서 측정값이 확정적인 기하학적 지식으로 잘못 해석되는 것을 방지할 수 있다.

의미론적 정보(Semantic Information)는 순수한 기하학적 표현을 더욱 확장한다. 포인트에는 클래스 레이블(Class Label)을 부여할 수 있고, 복셀에는 의미론적 확률 또는 학습 특징을 저장할 수 있으며, 메시 표면은 객체, 재질 또는 기능적 영역(Functional Region)과 연결될 수 있다. 기하학(Geometry)이 어떤 것이 어디에 존재하는지를 설명한다면, 의미론(Semantics)은 그것이 무엇인지 또는 어떻게 사용될 수 있는지를 설명한다. 이러한 결합은 상위 수준의 장면 이해와 피지컬 AI(Physical AI)를 위한 중요한 기반을 형성한다.

로봇 플랫폼은 물리적 작업의 특성에 따라 서로 다른 표현을 강조한다. 실외 자율이동로봇(Outdoor AMR)은 장애물 검출과 주행 가능성 분석(Traversability Analysis)을 위해 포인트 클라우드와 점유 구조를 주로 활용할 수 있다. 사족보행 로봇(Quadruped)은 발 디딤 위치(Foothold)를 선택하기 위해 조밀한 국부 지형 기하학이 필요할 수 있다. 매니퓰레이터(Manipulator)는 정밀한 객체 표면과 자세 정보가 필요하며, 무인항공기(UAV)는 제한된 계산 자원으로 넓은 3차원 공간을 표현할 수 있는 희소 표현을 선호할 수 있다.

현대의 인지 아키텍처(Perception Architecture)는 하나의 공간 표현만 선택하기보다 여러 표현을 동시에 유지하는 방향으로 발전하고 있다. 원시 또는 필터링된 포인트 클라우드는 센서 관측 증거를 보존하고, 희소 복셀은 효율적인 신경망 추론을 지원하며, 점유 구조는 내비게이션을 지원하고, 메시 또는 재구성 표면은 정밀한 기하학적 추론을 지원한다. 학습된 특징 볼륨(Learned Feature Volume) 역시 이러한 구조와 함께 존재하면서 기하학만으로 표현하기 어려운 작업 중심 정보를 제공할 수 있다.

따라서 올바른 공간 표현은 로봇의 행동 요구사항(Action Requirement), 센서 구성(Sensor Configuration), 운용 공간의 규모, 지연시간 예산(Latency Budget), 사용 가능한 계산 자원에 의해 결정된다. 포인트 클라우드는 유연한 샘플 기반 기하학을 제공하고, 복셀은 구조화된 체적 추론(Volumetric Reasoning)을 제공하며, 메시는 연결된 표면 모델(Connected Surface Model)을 제공한다. 실제 양산 시스템(Production System)은 각각의 기하학적 특성과 계산적 특성이 가장 유용한 위치에서 활용될 수 있도록 이들을 함께 사용하는 경우가 많다.

공간 표현은 궁극적으로 센싱(Sensing)과 물리적 추론(Physical Reasoning)을 연결하는 다리 역할을 한다. 센서는 환경의 일부만을 단편적으로 관측하지만, 자율 로봇은 자유 공간, 장애물, 표면, 객체 및 가능한 상호작용을 판단하기 위해 구조화된 공간 지식(Spatial Knowledge)을 필요로 한다. 포인트 클라우드, 복셀, 메시 및 관련 구조를 적절하게 선택하고 결합함으로써 인지 시스템은 단편적인 측정값을 신뢰할 수 있는 자율 행동(Autonomous Action)을 지원하는 공간 모델로 변환할 수 있다.

##  

## 01.04. Semantic Representations Labels Embeddings Graphs

![](images/image4.png){width="7.268055555555556in" height="7.268055555555556in"}

Semantic representation extends robot perception beyond geometry by describing what observed entities mean, how they relate to one another, and how they may matter for future actions. While spatial representations encode where surfaces, obstacles, and objects exist, semantic representations attach concepts to those structures. Labels, learned embeddings, and graphs provide complementary mechanisms for organizing this meaning at different levels of abstraction.

A semantic label assigns an explicit category or property to an observation. Pixels may be labeled as road, wall, person, vegetation, or floor, while points and voxels can receive equivalent three-dimensional classes. Object-level labels identify entities such as pallets, vehicles, tools, doors, or charging stations. Labels transform numerical sensor measurements into discrete concepts that downstream robot functions can interpret directly.

Labels are particularly useful because they are compact and human interpretable. A navigation system can assign different costs to floor, grass, stairs, and restricted zones, while a manipulation system can select behavior according to whether an observed object is a box, bottle, handle, or tool. Semantic maps can similarly associate regions with concepts such as corridor, loading zone, workstation, storage area, or hazardous region.

The simplicity of categorical labels also creates limitations. A fixed class vocabulary assumes that relevant concepts are known when the perception system is designed or trained. Objects outside that vocabulary may be classified incorrectly, grouped into a generic unknown category, or ignored. Labels also compress rich visual and geometric information into discrete symbols, potentially removing similarities and attributes that could be useful for reasoning about unfamiliar entities.

Learned embeddings provide a more continuous semantic representation. A neural network transforms an image region, object, point set, language description, or multimodal observation into a vector in a learned feature space. Rather than representing an entity only by a discrete class identifier, the embedding encodes patterns that allow semantically or visually related observations to occupy related regions of the representation space.

This continuous structure makes embeddings useful for recognition, retrieval, association, and open-vocabulary perception. Two chairs with substantially different appearances can produce related representations even when their pixel values differ greatly. Vision-language embeddings can additionally connect visual observations with textual concepts, enabling a robot to search for an object described by language even when that exact category was not explicitly included in a conventional closed-set classifier.

Embeddings can represent more than object identity. They may encode appearance, geometry, material, context, affordance, motion, or task-related properties depending on the training objective and input modalities. Point-cloud features can capture local three-dimensional structure, image embeddings can preserve visual semantics, and multimodal features can combine geometry, appearance, language, and temporal information into a shared latent representation.

The flexibility of embeddings comes with reduced interpretability. Individual dimensions usually do not correspond to clearly defined human concepts, and similar vectors do not automatically guarantee identical physical properties or safe actions. Embedding quality also depends strongly on training data and objectives. Domain shift, unusual viewpoints, sensor degradation, or novel environments can therefore alter feature relationships in ways that are difficult to diagnose directly.

Semantic graphs add another level by explicitly representing relationships among entities. In a graph, nodes can correspond to objects, places, regions, people, surfaces, or abstract concepts, while edges describe relationships such as inside, on, adjacent to, connected to, supports, near, moving toward, or reachable from. A graph therefore describes not only which entities exist but also how they are organized within a scene.

A robot observing a warehouse might construct nodes for an aisle, pallet, rack, forklift, and loading zone. Edges could indicate that a pallet is beside a rack, the rack is located within an aisle, and the aisle connects to a loading zone. Such relational structure provides information that cannot be represented adequately by independent object labels alone and can support task planning, semantic navigation, and contextual reasoning.

Scene graphs can combine semantic relationships with spatial attributes. Each node may contain a class label, position, dimensions, confidence, learned embedding, or object state, while edges may contain geometric distances, relative poses, interaction relationships, or temporal dependencies. The resulting structure bridges metric representations of physical space with symbolic descriptions that higher-level reasoning systems can manipulate more efficiently.

Graphs can also represent environments hierarchically. Individual objects may belong to regions, regions may form rooms or zones, and zones may belong to larger facilities. Such hierarchical semantic representations allow a robot to reason at different scales. A navigation request such as "go to the charging station in the maintenance area" can first be interpreted at the facility level and subsequently refined into local geometric navigation.

Temporal relationships extend semantic graphs into dynamic scene representations. Nodes can persist as objects are observed across multiple frames, while edges change when relationships evolve. A person may move from beside a vehicle to in front of it, or a pallet may change from stored to being transported. Maintaining these changes allows perception to describe not only the current scene but also its evolving state and interactions.

Labels, embeddings, and graphs therefore operate at complementary abstraction levels rather than serving as mutually exclusive alternatives. A single object node in a scene graph may contain a discrete semantic label, a learned embedding, a three-dimensional position, an estimated velocity, and an uncertainty value. Its graph edges can then describe relationships with surrounding entities. Rich robot perception often combines all three representation forms simultaneously.

Semantic representations become even more powerful when connected to spatial representations. Semantic labels can be attached to image pixels, point-cloud samples, voxels, occupancy cells, or mesh surfaces. Embeddings can be stored in spatial feature maps or three-dimensional feature volumes. Scene-graph nodes can reference metric object poses and mapped regions. Geometry answers where an entity is, while semantics describes what it is and how it relates to the robot\'s task.

Uncertainty remains important because semantic interpretation is rarely absolute. Classification probabilities, embedding similarity scores, object existence confidence, and uncertain graph relationships should be preserved when possible. A robot should distinguish between a confirmed pedestrian and a weak hypothesis, or between a known charging station and an object that merely appears semantically similar. Such uncertainty can influence planning and safety decisions.

Open-set and open-vocabulary perception increase the importance of flexible semantic representations. Fixed labels remain efficient for known operational categories, but embeddings allow perception to compare observations against new textual or visual concepts. Graph structures can then incorporate newly recognized entities without requiring the entire environmental representation to be redesigned. This combination supports robots operating in environments containing previously unseen objects and situations.

For Physical AI, semantics must ultimately connect perception with action. Recognizing an entity as a cup is useful, but understanding that it is graspable, contains liquid, is located on a table, and is requested by a human provides substantially richer information for behavior. Semantic representations can therefore include affordances, functional relationships, task relevance, and interaction possibilities rather than limiting understanding to conventional object categories.

Different robot platforms require different semantic complexity. An industrial AMR may primarily need semantic zones, obstacles, humans, and infrastructure labels. A manipulator requires object identity, pose, parts, affordances, and relationships. A humanoid or general-purpose Physical AI system may require open-vocabulary embeddings and dynamic scene graphs capable of connecting language instructions with objects, locations, people, and possible actions.

Semantic representation consequently forms a bridge between low-level perception and higher-level intelligence. Labels provide explicit and efficient categories, embeddings preserve continuous learned meaning, and graphs organize entities and their relationships into structured knowledge. Combined with spatial and temporal representations, they allow a robot to move from detecting physical structures toward understanding scenes in terms that can support reasoning, planning, interaction, and autonomous action.

의미론적 표현(Semantic Representation)은 관측된 개체가 무엇을 의미하는지, 서로 어떤 관계를 가지는지, 그리고 향후 행동에 어떤 의미를 가질 수 있는지를 기술함으로써 로봇 인지(Robot Perception)를 기하학적 정보 이상으로 확장한다. 공간 표현(Spatial Representation)이 표면, 장애물, 객체가 어디에 존재하는지를 표현한다면, 의미론적 표현은 이러한 구조에 개념적 의미를 부여한다. 레이블(Label), 학습된 임베딩(Learned Embedding), 그래프(Graph)는 서로 다른 추상화 수준에서 이러한 의미를 구성하는 상호 보완적인 방법을 제공한다.

의미론적 레이블(Semantic Label)은 관측 대상에 명시적인 범주 또는 속성을 할당한다. 픽셀에는 도로, 벽, 사람, 식생, 바닥 등의 레이블을 부여할 수 있으며, 포인트와 복셀(Voxel)에도 동일한 방식으로 3차원 클래스를 할당할 수 있다. 객체 수준 레이블(Object-Level Label)은 팔레트, 차량, 도구, 문, 충전 스테이션과 같은 개체를 식별한다. 레이블은 수치 형태의 센서 측정값을 후속 로봇 기능이 직접 해석할 수 있는 이산적인 개념(Discrete Concept)으로 변환한다.

레이블은 압축적이고 사람이 쉽게 해석할 수 있다는 점에서 특히 유용하다. 내비게이션 시스템(Navigation System)은 바닥, 잔디, 계단, 제한 구역에 서로 다른 비용을 할당할 수 있으며, 조작 시스템(Manipulation System)은 관측된 객체가 상자, 병, 손잡이 또는 도구인지에 따라 서로 다른 행동을 선택할 수 있다. 의미 지도(Semantic Map) 역시 복도, 적재 구역, 작업대, 저장 구역, 위험 구역과 같은 개념을 특정 공간 영역과 연결할 수 있다.

범주형 레이블(Categorical Label)의 단순성은 동시에 한계를 만든다. 고정된 클래스 어휘(Fixed Class Vocabulary)는 인지 시스템이 설계되거나 학습될 때 관련 개념이 이미 알려져 있다고 가정한다. 이러한 어휘에 포함되지 않은 객체는 잘못 분류되거나 일반적인 미확인(Unknown) 범주로 분류되거나 완전히 무시될 수 있다. 또한 레이블은 풍부한 시각적·기하학적 정보를 이산적인 기호로 압축하므로, 익숙하지 않은 개체를 추론할 때 유용할 수 있는 유사성과 속성을 제거할 가능성이 있다.

학습된 임베딩(Learned Embedding)은 보다 연속적인 의미론적 표현(Continuous Semantic Representation)을 제공한다. 신경망(Neural Network)은 이미지 영역, 객체, 포인트 집합, 언어 설명 또는 다중모달 관측(Multimodal Observation)을 학습된 특징 공간(Learned Feature Space)의 벡터로 변환한다. 개체를 하나의 이산적인 클래스 식별자로만 표현하는 대신, 임베딩은 의미적 또는 시각적으로 관련된 관측이 표현 공간에서 서로 가까운 영역에 위치하도록 하는 패턴을 인코딩한다.

이러한 연속적인 구조는 임베딩을 인식(Recognition), 검색(Retrieval), 연관(Association), 개방형 어휘 인지(Open-Vocabulary Perception)에 유용하게 만든다. 외형이 크게 다른 두 개의 의자도 픽셀 값이 상당히 다르더라도 서로 관련된 표현을 생성할 수 있다. 비전-언어 임베딩(Vision-Language Embedding)은 시각적 관측을 텍스트 개념과 연결하여, 기존의 폐쇄형 분류기(Closed-Set Classifier)에 해당 범주가 명시적으로 포함되지 않았더라도 로봇이 언어로 설명된 객체를 검색할 수 있도록 한다.

임베딩은 객체의 정체성(Object Identity) 이상의 정보를 표현할 수 있다. 학습 목적과 입력 모달리티(Input Modality)에 따라 외형, 기하학, 재질, 문맥(Context), 어포던스(Affordance), 움직임 또는 작업 관련 속성을 인코딩할 수 있다. 포인트 클라우드 특징(Point-Cloud Feature)은 국부적인 3차원 구조를 포착하고, 이미지 임베딩(Image Embedding)은 시각적 의미를 보존하며, 다중모달 특징(Multimodal Feature)은 기하학, 외형, 언어 및 시간 정보를 공유 잠재 표현(Shared Latent Representation)으로 결합할 수 있다.

임베딩의 유연성에는 낮은 해석 가능성(Interpretability)이라는 대가가 따른다. 개별 차원은 일반적으로 사람이 명확하게 정의할 수 있는 특정 개념과 직접 대응하지 않으며, 서로 유사한 벡터라고 해서 반드시 동일한 물리적 특성이나 안전한 행동을 보장하는 것도 아니다. 또한 임베딩 품질은 학습 데이터와 학습 목적에 크게 의존한다. 따라서 도메인 변화(Domain Shift), 비정상적인 관측 시점, 센서 성능 저하 또는 새로운 환경은 직접 진단하기 어려운 방식으로 특징 간 관계를 변화시킬 수 있다.

의미론적 그래프(Semantic Graph)는 개체 사이의 관계를 명시적으로 표현함으로써 또 다른 수준의 표현을 제공한다. 그래프에서 노드(Node)는 객체, 장소, 영역, 사람, 표면 또는 추상적인 개념에 대응할 수 있으며, 엣지(Edge)는 내부에 있음(Inside), 위에 있음(On), 인접함(Adjacent), 연결됨(Connected), 지지함(Supports), 가까움(Near), 접근 중임(Moving Toward), 도달 가능함(Reachable From) 등의 관계를 표현한다. 따라서 그래프는 어떤 개체가 존재하는지를 넘어 장면 안에서 개체들이 어떻게 구성되어 있는지를 설명한다.

창고를 관측하는 로봇은 통로(Aisle), 팔레트(Pallet), 랙(Rack), 지게차(Forklift), 적재 구역(Loading Zone)을 각각 노드로 구성할 수 있다. 엣지는 팔레트가 랙 옆에 있고, 랙이 통로 내부에 위치하며, 해당 통로가 적재 구역과 연결되어 있음을 나타낼 수 있다. 이러한 관계 구조(Relational Structure)는 독립적인 객체 레이블만으로는 충분히 표현하기 어려운 정보를 제공하며 작업 계획(Task Planning), 의미론적 내비게이션(Semantic Navigation), 문맥적 추론(Contextual Reasoning)을 지원할 수 있다.

장면 그래프(Scene Graph)는 의미론적 관계와 공간 속성(Spatial Attribute)을 결합할 수 있다. 각 노드는 클래스 레이블, 위치, 크기, 신뢰도, 학습된 임베딩 또는 객체 상태를 포함할 수 있으며, 엣지는 기하학적 거리, 상대 자세(Relative Pose), 상호작용 관계 또는 시간적 의존성(Temporal Dependency)을 포함할 수 있다. 이렇게 구성된 구조는 물리 공간의 계량적 표현(Metric Representation)과 상위 수준 추론 시스템이 효율적으로 처리할 수 있는 기호적 설명(Symbolic Description)을 연결한다.

그래프는 환경을 계층적(Hierarchical)으로 표현할 수도 있다. 개별 객체는 특정 영역에 속할 수 있고, 여러 영역은 방이나 구역을 형성하며, 이러한 구역들은 다시 더 큰 시설의 일부가 될 수 있다. 이러한 계층적 의미론적 표현(Hierarchical Semantic Representation)을 이용하면 로봇은 서로 다른 공간 규모에서 추론할 수 있다. 예를 들어 "정비 구역에 있는 충전 스테이션으로 이동하라"는 내비게이션 요청은 먼저 시설 수준에서 해석된 후 국부적인 기하학 내비게이션으로 세분화될 수 있다.

시간적 관계(Temporal Relationship)는 의미론적 그래프를 동적 장면 표현(Dynamic Scene Representation)으로 확장한다. 객체가 여러 프레임에 걸쳐 관측되는 동안 노드는 지속적으로 유지될 수 있으며, 관계가 변화하면 엣지도 변경될 수 있다. 사람이 차량 옆에서 차량 앞으로 이동하거나, 팔레트가 보관 상태에서 운반 상태로 변경될 수 있다. 이러한 변화를 유지하면 인지 시스템은 현재 장면뿐 아니라 시간에 따라 변화하는 상태와 상호작용까지 표현할 수 있다.

따라서 레이블, 임베딩, 그래프는 서로 배타적인 대안이 아니라 상호 보완적인 추상화 수준에서 동작한다. 하나의 장면 그래프 객체 노드는 이산적인 의미론적 레이블, 학습된 임베딩, 3차원 위치, 추정 속도 및 불확실성 값을 동시에 포함할 수 있다. 그리고 그래프의 엣지는 주변 개체와의 관계를 표현할 수 있다. 풍부한 로봇 인지는 이러한 세 가지 표현 방식을 동시에 결합하는 경우가 많다.

의미론적 표현은 공간 표현과 연결될 때 더욱 강력해진다. 의미론적 레이블은 이미지 픽셀, 포인트 클라우드 샘플, 복셀, 점유 셀(Occupancy Cell), 메시 표면(Mesh Surface)에 연결할 수 있다. 임베딩은 공간 특징 맵(Spatial Feature Map)이나 3차원 특징 볼륨(3D Feature Volume)에 저장할 수 있으며, 장면 그래프의 노드는 계량적 객체 자세(Metric Object Pose) 및 지도 영역과 연결할 수 있다. 기하학이 개체가 어디에 있는지를 설명한다면 의미론은 그것이 무엇이며 로봇의 작업과 어떤 관계가 있는지를 설명한다.

의미론적 해석은 완전히 확정적이지 않은 경우가 많으므로 불확실성(Uncertainty) 역시 중요하다. 분류 확률(Classification Probability), 임베딩 유사도 점수(Embedding Similarity Score), 객체 존재 신뢰도(Object Existence Confidence), 불확실한 그래프 관계를 가능한 한 유지해야 한다. 로봇은 확실하게 확인된 보행자와 낮은 신뢰도의 가설을 구분하고, 알려진 충전 스테이션과 의미적으로 유사해 보이는 객체를 구분할 수 있어야 한다. 이러한 불확실성은 경로 계획과 안전 관련 판단에 영향을 줄 수 있다.

개방형 집합 인지(Open-Set Perception)와 개방형 어휘 인지(Open-Vocabulary Perception)는 유연한 의미론적 표현의 중요성을 더욱 증가시킨다. 고정된 레이블은 이미 알려진 운용 범주를 효율적으로 처리할 수 있지만, 임베딩은 관측 결과를 새로운 텍스트 또는 시각적 개념과 비교할 수 있게 한다. 그래프 구조는 전체 환경 표현을 다시 설계하지 않고도 새롭게 인식된 개체를 추가할 수 있다. 이러한 결합은 이전에 관측하지 못한 객체와 상황이 존재하는 환경에서 로봇이 동작할 수 있도록 지원한다.

피지컬 AI(Physical AI)에서 의미론은 궁극적으로 인지와 행동(Action)을 연결해야 한다. 어떤 개체를 컵으로 인식하는 것도 유용하지만, 해당 컵이 파지 가능하고(Graspable), 액체를 담고 있으며, 테이블 위에 놓여 있고, 사람이 요청한 대상이라는 사실을 이해하면 행동을 결정하는 데 훨씬 풍부한 정보를 제공한다. 따라서 의미론적 표현은 기존의 객체 범주에만 제한되지 않고 어포던스, 기능적 관계(Functional Relationship), 작업 관련성(Task Relevance), 상호작용 가능성(Interaction Possibility)을 포함할 수 있다.

로봇 플랫폼마다 요구되는 의미론적 복잡성(Semantic Complexity)은 서로 다르다. 산업용 자율이동로봇(Industrial AMR)은 주로 의미론적 구역, 장애물, 사람 및 인프라 레이블이 필요할 수 있다. 매니퓰레이터(Manipulator)는 객체 식별, 자세, 구성 부품, 어포던스 및 객체 간 관계를 필요로 한다. 휴머노이드(Humanoid) 또는 범용 피지컬 AI 시스템(General-Purpose Physical AI System)은 언어 명령을 객체, 위치, 사람 및 가능한 행동과 연결할 수 있는 개방형 어휘 임베딩과 동적 장면 그래프(Dynamic Scene Graph)를 요구할 수 있다.

결과적으로 의미론적 표현은 저수준 인지(Low-Level Perception)와 상위 수준 지능(Higher-Level Intelligence)을 연결하는 다리 역할을 한다. 레이블은 명시적이고 효율적인 범주를 제공하고, 임베딩은 연속적으로 학습된 의미를 보존하며, 그래프는 개체와 그 관계를 구조화된 지식(Structured Knowledge)으로 구성한다. 이러한 표현을 공간 및 시간 표현과 결합하면 로봇은 물리적 구조를 단순히 검출하는 수준을 넘어 추론(Reasoning), 계획(Planning), 상호작용(Interaction), 자율 행동(Autonomous Action)을 지원할 수 있는 형태로 장면을 이해할 수 있다.

##  

## 01.05. Perception Latency Budget for Real Time Robots

![](images/image5.png){width="7.268055555555556in" height="7.268055555555556in"}

Real-time robot perception is constrained not only by accuracy but also by how quickly sensor information can be transformed into information suitable for decision and action. A perception result that arrives too late may describe a world that has already changed. Latency budgeting therefore allocates allowable processing time across sensing, transfer, preprocessing, inference, fusion, interpretation, and communication so the complete perception pipeline satisfies the robot\'s response-time requirement.

Perception latency should be considered from measurement acquisition to availability of the final output rather than from neural-network inference alone. The pipeline may include sensor exposure or scanning, device buffering, data transfer, decoding, synchronization, calibration, preprocessing, GPU upload, inference, postprocessing, sensor fusion, tracking, and message publication. Each stage contributes delay, and several small delays can accumulate into a significant end-to-end latency.

A useful latency budget begins with the physical behavior that the robot must support. Vehicle speed, braking capability, obstacle distance, manipulator motion, control frequency, and safety margin determine how old perception information can become before it is no longer useful. A fast outdoor robot generally requires tighter response constraints than a slowly moving warehouse platform, while high-speed manipulation or legged locomotion can require particularly rapid local perception updates.

Sensor acquisition introduces unavoidable latency before computation begins. A camera may require exposure and readout time, a rotating LiDAR needs time to collect points across a scan, and radar processing depends on its measurement cycle. Sensor data may also remain temporarily in hardware or driver buffers. Consequently, the timestamp of a completed sensor message can differ substantially from the physical time at which different portions of that measurement were actually observed.

Transport latency occurs when sensor data moves through interfaces such as Ethernet, USB, MIPI, PCIe, or middleware communication. Large camera frames and dense point clouds can consume considerable bandwidth, while unnecessary memory copies increase delay and processor load. Zero-copy transport, direct memory access, efficient serialization, and careful placement of processing nodes can reduce data movement and preserve more of the latency budget for actual perception computation.

Preprocessing consumes another portion of the budget. Image rectification, resizing, normalization, point-cloud filtering, voxelization, radar signal processing, coordinate transformation, and sensor synchronization may all occur before AI inference. Although each operation can appear inexpensive in isolation, sequential preprocessing across multiple sensors can become substantial. Efficient implementations therefore combine operations, exploit parallelism, and avoid repeated conversion between data formats.

Neural-network inference is often the most visible computational component, but its latency depends on more than model size. Input resolution, batch size, precision, memory bandwidth, accelerator utilization, kernel scheduling, and data-transfer overhead all influence execution time. GPU, DLA, and NPU accelerators can reduce inference latency, but the benefit is limited if the surrounding pipeline remains dominated by CPU preprocessing, synchronization, or memory movement.

Postprocessing must also be included in the latency budget. Detection networks may require decoding, thresholding, non-maximum suppression, coordinate conversion, or clustering before their outputs become usable. Three-dimensional perception can require additional geometric transformations, while tracking and occupancy generation introduce further computation. Measuring inference time alone can therefore substantially underestimate the delay experienced by navigation, planning, or control modules.

Multi-sensor fusion creates a special latency problem because sensors rarely operate at identical frequencies or measurement times. Waiting for perfectly synchronized camera, LiDAR, radar, and IMU observations may improve temporal alignment but can increase response delay. Using the newest available measurement reduces waiting time but introduces temporal offsets. Production systems must therefore balance synchronization accuracy against freshness and may compensate measurements using estimated ego-motion.

Latency and throughput should not be treated as equivalent metrics. A perception system may process thirty frames per second while still having a large delay between image capture and output if several frames remain queued in the pipeline. Throughput describes how much data can be processed over time, whereas latency describes how long one observation takes to reach its consumer. Real-time robotics requires both adequate throughput and sufficiently low end-to-end latency.

Queueing is particularly dangerous because latency can increase without obvious reductions in average processing rate. If sensors generate data faster than the perception pipeline can consume it, messages accumulate and the robot begins processing increasingly old observations. For many real-time applications, dropping stale frames and processing the newest available observation is preferable to preserving every frame. Queue depth and back-pressure policy are therefore architectural parameters rather than minor implementation details.

Average latency alone is also insufficient for real-time design. A system that usually responds quickly but occasionally experiences large timing spikes may create unsafe or unstable behavior. Percentile latency, maximum observed latency, and worst-case execution behavior are therefore important. Memory allocation, operating-system scheduling, thermal throttling, GPU contention, logging, and background processes can create jitter that is invisible when only average inference time is reported.

Pipeline parallelism can improve performance by allowing different stages to process different observations simultaneously. While the GPU performs inference on one frame, the CPU can preprocess the next sensor input and another thread can postprocess the previous result. This improves resource utilization and throughput, but excessive buffering can increase observation age. Parallel architecture must therefore be designed to reduce idle time without unintentionally creating deep queues.

Different perception outputs can operate under different latency budgets. Emergency obstacle detection may require the freshest possible information, while semantic classification or long-term map updates can tolerate larger delays. IMU-based motion estimation can operate at a high rate, object detection at a moderate rate, and detailed scene understanding at a lower rate. Multi-rate architecture prevents expensive semantic processing from unnecessarily delaying safety-critical geometric perception.

Spatial resolution and model complexity directly influence the latency budget. Higher-resolution images preserve small objects, dense point clouds preserve geometric detail, and fine voxels improve spatial precision, but all increase computation and memory traffic. Model pruning, quantization, reduced precision, sparse computation, region-of-interest processing, and adaptive resolution can reduce processing time while attempting to preserve the information most important for the robot\'s current task.

Latency can also be reduced by distributing computation appropriately across heterogeneous hardware. CPUs are effective for control-oriented logic and some preprocessing, GPUs provide highly parallel neural inference, and dedicated accelerators can execute selected networks efficiently. The optimal design minimizes unnecessary transfers among these devices. A theoretically faster accelerator can produce worse system latency if repeated memory copies or synchronization barriers dominate the execution path.

The age of information provides a useful system-level perspective. When a planner receives an obstacle position, the relevant question is not only how long the detection algorithm executed but how much time has passed since the underlying physical observation occurred. Sensor acquisition, buffering, synchronization, inference, fusion, and communication all contribute to this age. Dynamic-object motion can make even an accurate estimate increasingly incorrect as its information becomes older.

Temporal prediction can partially compensate for unavoidable latency. Tracking systems can propagate object states from their measurement timestamps toward the current time using estimated velocity or motion models. Ego-motion compensation can similarly transform older observations according to robot movement. Prediction does not eliminate latency, and prediction uncertainty grows with time, but it allows downstream modules to reason about the expected current state rather than blindly consuming stale measurements.

Safety engineering requires explicit behavior when the latency budget is violated. A perception system can monitor timestamps, processing duration, queue depth, sensor freshness, and output age. If information becomes too old, the robot may reduce speed, increase safety distance, reject the affected perception output, switch to a degraded sensing mode, or stop. Timing validity should therefore be treated as part of perception confidence rather than merely as a performance statistic.

Latency optimization must finally be performed at the complete robot-system level. Accelerating a detector from twenty milliseconds to ten milliseconds provides little benefit if sensor buffering, synchronization, and downstream communication still consume far more time. Profiling should identify the critical path from physical measurement to action-relevant output and optimize the dominant contributors while preserving accuracy, robustness, and maintainability.

A well-designed perception latency budget connects physical robot dynamics with computing architecture. Sensor rates, preprocessing, neural inference, fusion, communication, scheduling, and safety policies are allocated within a finite response-time envelope. The objective is not simply to achieve the highest frame rate, but to ensure that sufficiently accurate information reaches the robot predictably while it is still fresh enough to support safe and effective autonomous action.

실시간 로봇 인지(Real-Time Robot Perception)는 정확도뿐만 아니라 센서 정보를 얼마나 신속하게 판단과 행동에 적합한 정보로 변환할 수 있는지에 의해서도 제약을 받는다. 너무 늦게 도착한 인지 결과는 이미 변화한 환경을 설명하고 있을 수 있다. 따라서 지연시간 예산(Latency Budgeting)은 전체 인지 파이프라인이 로봇의 응답시간 요구사항을 충족하도록 센싱, 전송, 전처리, 추론, 융합, 해석 및 통신에 허용 가능한 처리 시간을 배분하는 과정이다.

인지 지연시간(Perception Latency)은 신경망 추론(Neural-Network Inference) 시간만이 아니라 측정 획득부터 최종 출력이 사용 가능한 상태가 될 때까지 전체 구간을 기준으로 고려해야 한다. 파이프라인에는 센서 노출 또는 스캐닝, 장치 버퍼링(Device Buffering), 데이터 전송, 디코딩, 동기화, 보정, 전처리, GPU 업로드, 추론, 후처리, 센서 융합, 추적 및 메시지 발행(Message Publication)이 포함될 수 있다. 각 단계가 지연을 발생시키며 여러 개의 작은 지연이 누적되면 상당한 종단간 지연시간(End-to-End Latency)이 될 수 있다.

유용한 지연시간 예산은 로봇이 수행해야 하는 물리적 동작(Physical Behavior)에서 시작한다. 차량 속도, 제동 성능, 장애물 거리, 매니퓰레이터 움직임, 제어 주기 및 안전 여유(Safety Margin)는 인지 정보가 더 이상 유효하지 않게 되기 전까지 얼마나 오래된 정보를 허용할 수 있는지를 결정한다. 빠르게 이동하는 실외 로봇은 일반적으로 저속 창고 로봇보다 더 엄격한 응답 제약이 필요하며, 고속 조작이나 보행 로봇의 이동은 특히 빠른 국부 인지 갱신을 요구할 수 있다.

센서 획득(Sensor Acquisition)은 계산이 시작되기 전부터 피할 수 없는 지연시간을 발생시킨다. 카메라는 노출 및 판독 시간(Readout Time)이 필요할 수 있고, 회전형 라이다(Rotating LiDAR)는 하나의 스캔에 걸쳐 포인트를 수집하는 시간이 필요하며, 레이더 처리는 자체 측정 주기(Measurement Cycle)에 영향을 받는다. 센서 데이터가 하드웨어 또는 드라이버 버퍼에 일시적으로 남아 있을 수도 있으므로 완성된 센서 메시지의 타임스탬프와 해당 측정값의 각 부분이 실제 물리적으로 관측된 시간 사이에는 상당한 차이가 발생할 수 있다.

전송 지연시간(Transport Latency)은 센서 데이터가 이더넷(Ethernet), USB, MIPI, PCIe 또는 미들웨어 통신(Middleware Communication) 등의 인터페이스를 통해 이동할 때 발생한다. 대용량 카메라 프레임과 고밀도 포인트 클라우드는 상당한 대역폭을 소비할 수 있으며, 불필요한 메모리 복사(Memory Copy)는 지연시간과 프로세서 부하를 증가시킨다. 제로 카피(Zero-Copy) 전송, 직접 메모리 접근(Direct Memory Access), 효율적인 직렬화(Serialization), 적절한 처리 노드 배치는 데이터 이동량을 줄이고 실제 인지 계산에 더 많은 지연시간 예산을 확보할 수 있게 한다.

전처리(Preprocessing) 역시 지연시간 예산의 일부를 소비한다. 영상 보정(Image Rectification), 크기 조정, 정규화(Normalization), 포인트 클라우드 필터링, 복셀화(Voxelization), 레이더 신호처리, 좌표 변환 및 센서 동기화는 모두 AI 추론 이전에 수행될 수 있다. 각 연산은 개별적으로는 가벼워 보이지만 여러 센서에 대해 순차적으로 수행되면 상당한 처리 시간이 필요할 수 있다. 따라서 효율적인 구현에서는 연산을 결합하고 병렬성(Parallelism)을 활용하며 데이터 형식 사이의 반복적인 변환을 최소화한다.

신경망 추론은 가장 눈에 띄는 계산 요소인 경우가 많지만, 추론 지연시간은 단순히 모델 크기에 의해서만 결정되지 않는다. 입력 해상도, 배치 크기(Batch Size), 정밀도(Precision), 메모리 대역폭, 가속기 활용률(Accelerator Utilization), 커널 스케줄링(Kernel Scheduling), 데이터 전송 오버헤드가 모두 실행시간에 영향을 미친다. GPU, DLA, NPU와 같은 가속기는 추론 지연시간을 줄일 수 있지만 주변 파이프라인이 CPU 전처리, 동기화 또는 메모리 이동에 의해 지배된다면 전체적인 효과는 제한된다.

후처리(Postprocessing) 역시 지연시간 예산에 포함해야 한다. 객체 검출 네트워크는 출력이 실제로 사용 가능해지기 전에 디코딩, 임계값 처리(Thresholding), 비최대 억제(Non-Maximum Suppression), 좌표 변환 또는 클러스터링(Clustering)을 요구할 수 있다. 3차원 인지는 추가적인 기하학적 변환을 요구할 수 있으며, 추적과 점유 정보 생성(Occupancy Generation)도 추가적인 계산을 발생시킨다. 따라서 추론 시간만 측정하면 내비게이션, 경로 계획 또는 제어 모듈이 실제로 경험하는 지연시간을 상당히 과소평가할 수 있다.

다중 센서 융합(Multi-Sensor Fusion)은 센서들이 동일한 주기나 측정 시점에서 동작하지 않기 때문에 특별한 지연시간 문제를 발생시킨다. 완벽하게 동기화된 카메라, 라이다, 레이더, IMU 관측을 기다리면 시간적 정렬(Temporal Alignment)은 향상될 수 있지만 응답 지연은 증가할 수 있다. 반대로 가장 최근의 측정값을 사용하면 대기시간은 줄어들지만 시간적 오프셋(Temporal Offset)이 발생한다. 따라서 실제 시스템은 동기화 정확성과 정보의 최신성(Freshness) 사이에서 균형을 유지해야 하며, 추정된 자기 운동(Ego-Motion)을 이용하여 측정값을 보정할 수 있다.

지연시간과 처리량(Throughput)을 동일한 지표로 취급해서는 안 된다. 인지 시스템이 초당 30프레임을 처리하더라도 여러 프레임이 파이프라인의 큐(Queue)에 남아 있다면 영상 획득부터 출력까지 상당한 지연이 존재할 수 있다. 처리량은 일정 시간 동안 얼마나 많은 데이터를 처리할 수 있는지를 의미하지만, 지연시간은 하나의 관측값이 소비자에게 도달하는 데 걸리는 시간을 의미한다. 실시간 로보틱스(Real-Time Robotics)는 충분한 처리량과 낮은 종단간 지연시간을 모두 요구한다.

큐잉(Queueing)은 평균 처리 속도가 명확하게 감소하지 않더라도 지연시간을 증가시킬 수 있기 때문에 특히 위험하다. 센서가 인지 파이프라인이 처리할 수 있는 속도보다 빠르게 데이터를 생성하면 메시지가 누적되고 로봇은 점점 오래된 관측값을 처리하게 된다. 많은 실시간 응용에서는 모든 프레임을 보존하는 것보다 오래된 프레임(Stale Frame)을 폐기하고 가장 최근의 관측을 처리하는 것이 더 적절하다. 따라서 큐 깊이(Queue Depth)와 역압력 정책(Back-Pressure Policy)은 단순한 구현 세부사항이 아니라 아키텍처 파라미터이다.

평균 지연시간(Average Latency)만으로도 실시간 시스템을 충분히 설계할 수 없다. 대부분 빠르게 응답하지만 가끔 큰 시간 지연이 발생하는 시스템은 안전하지 않거나 불안정한 동작을 유발할 수 있다. 따라서 백분위 지연시간(Percentile Latency), 최대 관측 지연시간(Maximum Observed Latency), 최악 조건 실행 특성(Worst-Case Execution Behavior)이 중요하다. 메모리 할당, 운영체제 스케줄링, 열 스로틀링(Thermal Throttling), GPU 자원 경쟁, 로깅 및 백그라운드 프로세스는 평균 추론 시간만으로는 확인하기 어려운 지터(Jitter)를 발생시킬 수 있다.

파이프라인 병렬화(Pipeline Parallelism)는 서로 다른 단계가 서로 다른 관측값을 동시에 처리하도록 하여 성능을 향상시킬 수 있다. GPU가 하나의 프레임에 대해 추론을 수행하는 동안 CPU는 다음 센서 입력을 전처리하고 다른 스레드(Thread)는 이전 결과를 후처리할 수 있다. 이러한 방식은 자원 활용률과 처리량을 향상시키지만 지나친 버퍼링은 관측 정보의 수명(Age)을 증가시킬 수 있다. 따라서 병렬 아키텍처는 의도하지 않은 깊은 큐를 생성하지 않으면서 유휴 시간을 줄이도록 설계해야 한다.

서로 다른 인지 출력에는 서로 다른 지연시간 예산을 적용할 수 있다. 비상 장애물 검출(Emergency Obstacle Detection)은 가능한 한 최신의 정보가 필요하지만, 의미론적 분류(Semantic Classification)나 장기 지도 갱신(Long-Term Map Update)은 더 큰 지연을 허용할 수 있다. IMU 기반 운동 추정은 높은 주기로 동작하고, 객체 검출은 중간 주기로, 상세한 장면 이해는 더 낮은 주기로 동작할 수 있다. 다중 주기 아키텍처(Multi-Rate Architecture)는 계산량이 큰 의미론적 처리가 안전에 중요한 기하학적 인지를 불필요하게 지연시키는 것을 방지한다.

공간 해상도(Spatial Resolution)와 모델 복잡도(Model Complexity)는 지연시간 예산에 직접적인 영향을 준다. 높은 해상도의 영상은 작은 객체를 보존하고, 고밀도 포인트 클라우드는 기하학적 세부 정보를 유지하며, 작은 복셀은 공간 정밀도를 높이지만 모두 계산량과 메모리 트래픽을 증가시킨다. 모델 가지치기(Model Pruning), 양자화(Quantization), 저정밀도 연산(Reduced Precision), 희소 연산(Sparse Computation), 관심 영역 처리(Region-of-Interest Processing), 적응형 해상도(Adaptive Resolution)는 로봇의 현재 작업에 중요한 정보를 유지하면서 처리 시간을 줄이는 방법이 될 수 있다.

지연시간은 이기종 하드웨어(Heterogeneous Hardware)에 계산을 적절히 분배함으로써 줄일 수도 있다. CPU는 제어 중심 로직과 일부 전처리에 효과적이며, GPU는 고도의 병렬 신경망 추론에 적합하고, 전용 가속기(Dedicated Accelerator)는 특정 신경망을 효율적으로 실행할 수 있다. 최적의 설계는 이러한 장치 사이의 불필요한 데이터 전송을 최소화한다. 이론적으로 더 빠른 가속기라도 반복적인 메모리 복사나 동기화 장벽(Synchronization Barrier)이 실행 경로를 지배한다면 전체 시스템 지연시간은 오히려 악화될 수 있다.

정보 수명(Age of Information)은 시스템 수준에서 지연시간을 이해하는 데 유용한 관점을 제공한다. 경로 계획기가 장애물 위치를 전달받았을 때 중요한 것은 검출 알고리즘의 실행시간뿐 아니라 해당 정보의 기반이 되는 물리적 관측이 이루어진 이후 얼마나 많은 시간이 경과했는가이다. 센서 획득, 버퍼링, 동기화, 추론, 융합 및 통신은 모두 정보 수명에 영향을 준다. 동적 객체의 움직임으로 인해 정확했던 추정값도 시간이 지나 정보가 오래될수록 실제 상태와 점점 달라질 수 있다.

시간 예측(Temporal Prediction)은 피할 수 없는 지연시간을 부분적으로 보상할 수 있다. 추적 시스템(Tracking System)은 추정 속도나 운동 모델(Motion Model)을 이용하여 객체 상태를 측정 타임스탬프에서 현재 시점까지 전파할 수 있다. 자기 운동 보상(Ego-Motion Compensation)도 로봇의 이동량에 따라 과거 관측값을 현재 좌표계로 변환할 수 있다. 예측이 지연시간 자체를 제거하는 것은 아니며 시간이 증가할수록 예측 불확실성도 커지지만, 후속 모듈이 오래된 측정값을 그대로 사용하는 대신 예상되는 현재 상태를 기반으로 추론할 수 있게 한다.

안전 엔지니어링(Safety Engineering)은 지연시간 예산을 초과했을 때 수행할 동작을 명시적으로 정의해야 한다. 인지 시스템은 타임스탬프, 처리시간, 큐 깊이, 센서 정보의 최신성 및 출력 정보의 수명을 모니터링할 수 있다. 정보가 지나치게 오래되면 로봇은 속도를 낮추고, 안전거리를 증가시키고, 해당 인지 출력을 거부하거나, 성능 저하 센싱 모드(Degraded Sensing Mode)로 전환하거나, 정지할 수 있다. 따라서 시간적 유효성(Timing Validity)은 단순한 성능 통계가 아니라 인지 신뢰도(Perception Confidence)의 일부로 취급해야 한다.

지연시간 최적화(Latency Optimization)는 최종적으로 전체 로봇 시스템 수준에서 수행해야 한다. 검출기의 실행시간을 20밀리초에서 10밀리초로 단축하더라도 센서 버퍼링, 동기화 및 후속 통신에서 훨씬 더 많은 시간이 소비된다면 실제 효과는 제한적이다. 프로파일링(Profiling)은 물리적 측정에서 행동에 필요한 출력까지 이어지는 임계 경로(Critical Path)를 식별하고 정확도, 강건성(Robustness), 유지보수성을 보존하면서 지배적인 지연 요소를 최적화해야 한다.

잘 설계된 인지 지연시간 예산(Perception Latency Budget)은 로봇의 물리적 동역학(Physical Dynamics)과 컴퓨팅 아키텍처(Computing Architecture)를 연결한다. 센서 주기, 전처리, 신경망 추론, 융합, 통신, 스케줄링 및 안전 정책은 제한된 응답시간 범위(Response-Time Envelope) 안에서 배분된다. 목표는 단순히 가장 높은 프레임률(Frame Rate)을 달성하는 것이 아니라, 충분히 정확한 정보가 안전하고 효과적인 자율 행동(Autonomous Action)을 지원할 수 있을 만큼 최신 상태를 유지하면서 예측 가능한 시간 안에 로봇에 전달되도록 하는 것이다.

##  

## 01.06. Perception Evaluation Metrics mAP IoU ATE

![](images/image6.png){width="7.268055555555556in" height="7.268055555555556in"}

Perception evaluation determines whether a robot\'s interpretation of sensor data is sufficiently accurate, consistent, and reliable for its intended task. Because perception includes detection, segmentation, localization, mapping, and tracking, no single metric can characterize the complete system. Metrics such as mean Average Precision, Intersection over Union, and Absolute Trajectory Error quantify different aspects of performance and must be interpreted according to the perception function being evaluated.

Object detection evaluation begins by comparing predicted objects with ground-truth annotations. A prediction is normally considered a true positive when its class is correct and its spatial overlap with the corresponding ground-truth object exceeds a defined threshold. Predictions without valid matches become false positives, while ground-truth objects that are not detected become false negatives. These quantities provide the basis for precision, recall, and Average Precision calculations.

Precision measures how many reported detections are correct, whereas recall measures how many relevant ground-truth objects were successfully detected. High precision with low recall indicates that the detector produces few false alarms but misses many objects. High recall with low precision means that most objects are found but many incorrect detections are also generated. Robot applications often require an explicit trade-off between these two behaviors.

A detector produces confidence scores rather than a single fixed set of decisions. Changing the confidence threshold changes the balance between precision and recall. A precision-recall curve therefore evaluates detection behavior across many thresholds. Average Precision, or AP, summarizes this curve for a particular object class and evaluation criterion, providing a more informative measure than accuracy computed at only one confidence threshold.

Mean Average Precision, or mAP, aggregates AP values across multiple object classes and, depending on the benchmark, multiple overlap thresholds. It is widely used for evaluating two-dimensional and three-dimensional object detectors. However, the exact definition must always be reported because mAP calculated at a single IoU threshold is not equivalent to mAP averaged across a range of increasingly strict overlap thresholds.

Intersection over Union, or IoU, measures the spatial overlap between a predicted region and its ground-truth counterpart. For two-dimensional boxes or segmentation masks, IoU is the area of their intersection divided by the area of their union. A value of one represents perfect overlap, while zero represents no overlap. The same concept can be extended to three-dimensional bounding boxes or volumetric regions.

IoU serves both as an evaluation metric and as a matching criterion. During object-detection evaluation, a predicted box may be counted as correct only when its IoU with a ground-truth box exceeds a selected threshold. Increasing this threshold demands more accurate localization. Consequently, two detectors with similar classification capability can receive different scores when one predicts object boundaries or positions more precisely.

For semantic segmentation, IoU is usually calculated independently for each semantic class. Pixels predicted and annotated as the same class form the intersection, while all pixels belonging to either the prediction or ground truth form the union. Mean IoU, commonly written mIoU, averages these class-level values and provides a compact measure of segmentation quality across the semantic categories represented in the evaluation dataset.

Class imbalance must be considered when interpreting segmentation metrics. Large regions such as road, floor, wall, or background may dominate pixel-level accuracy, allowing a system to achieve a high score while performing poorly on small but important objects. Class-wise IoU and mIoU reduce this problem by exposing performance for individual categories, but safety-critical classes should still be inspected separately rather than hidden inside a global average.

Localization and SLAM require fundamentally different metrics because their outputs are trajectories or poses rather than object categories. Absolute Trajectory Error, or ATE, evaluates the difference between an estimated robot trajectory and a reference trajectory after applying an appropriate alignment procedure. It therefore measures accumulated global trajectory consistency and is commonly used to evaluate visual odometry, LiDAR odometry, and SLAM systems.

ATE is often derived from translational differences between corresponding estimated and ground-truth poses and summarized using statistics such as root mean square error. Before comparison, trajectories may require alignment because estimated and reference coordinate frames can differ. Depending on the system, alignment may compensate for rigid transformation, and monocular systems can additionally require scale alignment because absolute scale may not be directly observable.

ATE should not be interpreted without considering trajectory duration and operating environment. An error that is acceptable over a long outdoor route may be unacceptable for precise indoor docking or manipulation. Two systems can also produce similar final ATE values while exhibiting very different temporal behavior. Evaluation should therefore inspect trajectory plots, local errors, failure events, and drift patterns in addition to a single aggregate number.

Relative Pose Error, or RPE, complements ATE by measuring the error in relative motion over a specified time or distance interval. ATE emphasizes global trajectory agreement, whereas RPE reveals local translational and rotational drift. A localization system can therefore have relatively small short-term motion error while accumulating substantial global drift, or it can exhibit locally unstable estimates even when global corrections eventually reduce ATE.

Tracking introduces additional evaluation requirements because identity must remain consistent over time. Detection quality alone cannot reveal whether a tracker repeatedly changes an object\'s identity or loses and reacquires tracks. Tracking metrics therefore consider localization, association, missed detections, false tracks, and identity consistency. For autonomous robots, temporal stability can be as important as frame-level detection accuracy because planners consume continuously evolving object states.

Metric selection must reflect the downstream physical task. A high mAP value does not guarantee safe navigation if the detector misses small nearby obstacles, and a high mIoU does not guarantee useful traversability estimation if errors occur primarily at critical boundaries. Similarly, low ATE does not necessarily guarantee stable control if localization contains short but severe pose jumps. Evaluation must connect statistical scores to the consequences of perception errors.

Operating conditions should also be represented explicitly in evaluation. Performance can change with distance, object size, velocity, illumination, weather, occlusion, sensor noise, terrain, and environmental complexity. Reporting only an aggregate score can hide severe weaknesses in difficult conditions. A production robot should therefore evaluate metrics across meaningful subsets that correspond to its operational design domain and expected failure scenarios.

Uncertainty and confidence calibration provide another dimension of evaluation. A perception system should ideally be less confident when observations are ambiguous or outside its normal operating distribution. A detector with slightly lower mAP but well-calibrated confidence can sometimes support safer decision-making than a more accurate model that remains highly confident when wrong. Reliability diagrams and calibration errors can therefore complement conventional accuracy metrics.

Latency must also be evaluated together with accuracy. A detector can achieve excellent mAP while requiring too much processing time for a moving robot, and a SLAM system can produce accurate trajectories while failing to update poses at the required control rate. Practical evaluation should therefore report perception quality together with end-to-end latency, update frequency, computational load, memory consumption, and timing stability on the intended deployment hardware.

Benchmark results are most useful when evaluation protocols are reproducible. Dataset version, class definitions, IoU thresholds, confidence handling, trajectory alignment, coordinate conventions, ignored regions, and averaging procedures should be documented. Small differences in evaluation configuration can produce significantly different numerical results, making direct comparison misleading when protocols are not equivalent.

A complete robot perception evaluation should consequently use a collection of complementary metrics rather than optimize one headline number. mAP characterizes object-detection performance, IoU and mIoU quantify spatial overlap and segmentation quality, while ATE and related trajectory metrics evaluate localization consistency. Tracking, calibration, latency, robustness, and task-specific safety measures extend this evaluation toward real deployment requirements.

The purpose of perception metrics is ultimately not to maximize benchmark scores but to determine whether the robot receives information of sufficient quality to act reliably. Meaningful evaluation connects numerical metrics with operating conditions, timing constraints, uncertainty, and physical consequences. When interpreted together, mAP, IoU, ATE, and complementary measures provide a structured framework for converting perception performance into engineering evidence for autonomous robot design.

인지 평가(Perception Evaluation)는 로봇이 센서 데이터를 해석한 결과가 목표 작업을 수행하기에 충분한 정확성, 일관성 및 신뢰성을 갖추고 있는지를 판단하는 과정이다. 인지는 객체 검출(Object Detection), 분할(Segmentation), 위치추정(Localization), 매핑(Mapping), 추적(Tracking) 등을 포함하기 때문에 하나의 지표만으로 전체 시스템의 성능을 평가할 수 없다. 평균 정밀도 평균(mean Average Precision, mAP), 교집합 대비 합집합(Intersection over Union, IoU), 절대 궤적 오차(Absolute Trajectory Error, ATE) 등의 지표는 서로 다른 성능 특성을 정량화하며, 평가 대상 인지 기능에 따라 해석해야 한다.

객체 검출 평가는 예측된 객체(Predicted Object)를 정답 주석(Ground-Truth Annotation)과 비교하는 것에서 시작한다. 일반적으로 예측 객체의 클래스가 정확하고 해당 정답 객체와의 공간적 중첩이 정의된 임계값 이상이면 참 양성(True Positive)으로 판단한다. 유효한 대응 객체가 없는 예측은 거짓 양성(False Positive)이 되고, 검출되지 않은 정답 객체는 거짓 음성(False Negative)이 된다. 이러한 값은 정밀도(Precision), 재현율(Recall), 평균 정밀도(Average Precision)를 계산하는 기반이 된다.

정밀도(Precision)는 시스템이 보고한 검출 결과 중 얼마나 많은 결과가 정확한지를 측정하고, 재현율(Recall)은 실제로 존재하는 정답 객체 중 얼마나 많은 객체를 성공적으로 검출했는지를 측정한다. 높은 정밀도와 낮은 재현율은 잘못된 경보는 적지만 많은 객체를 놓친다는 것을 의미한다. 반대로 높은 재현율과 낮은 정밀도는 대부분의 객체를 찾지만 잘못된 검출도 많이 발생한다는 의미이다. 로봇 응용에서는 이러한 두 특성 사이의 절충(Trade-Off)을 명확하게 고려해야 한다.

객체 검출기는 하나의 고정된 판단 결과가 아니라 신뢰도 점수(Confidence Score)를 생성한다. 신뢰도 임계값을 변경하면 정밀도와 재현율 사이의 균형도 달라진다. 따라서 정밀도-재현율 곡선(Precision-Recall Curve)은 다양한 임계값에서 객체 검출기의 동작을 평가한다. 평균 정밀도(Average Precision, AP)는 특정 객체 클래스와 평가 기준에 대해 이 곡선을 요약하며, 하나의 신뢰도 임계값에서 계산한 정확도보다 더 많은 정보를 제공한다.

평균 정밀도 평균(mean Average Precision, mAP)은 여러 객체 클래스의 AP 값을 통합하며, 벤치마크에 따라 여러 공간 중첩 임계값(Overlap Threshold)에 대한 결과까지 함께 평균할 수 있다. mAP는 2차원 및 3차원 객체 검출기의 평가에 널리 사용된다. 그러나 하나의 IoU 임계값에서 계산된 mAP와 점점 엄격해지는 여러 IoU 임계값 범위에서 평균한 mAP는 동일하지 않기 때문에 정확한 평가 정의를 항상 함께 제시해야 한다.

교집합 대비 합집합(Intersection over Union, IoU)은 예측 영역과 해당 정답 영역 사이의 공간적 중첩 정도를 측정한다. 2차원 경계 상자(Bounding Box) 또는 분할 마스크(Segmentation Mask)의 경우 IoU는 두 영역의 교집합 면적을 합집합 면적으로 나눈 값이다. 1은 완벽한 중첩을 의미하고 0은 중첩이 전혀 없음을 의미한다. 동일한 개념을 3차원 경계 상자 또는 체적 영역(Volumetric Region)으로 확장할 수도 있다.

IoU는 평가 지표인 동시에 객체 대응을 결정하는 기준(Matching Criterion)으로도 사용된다. 객체 검출 평가에서는 예측 경계 상자의 IoU가 선택된 임계값을 초과할 때만 올바른 검출로 판단할 수 있다. 임계값을 높일수록 더 정확한 위치추정이 요구된다. 따라서 분류 능력이 비슷한 두 검출기라도 하나의 검출기가 객체의 경계나 위치를 더욱 정확하게 예측한다면 서로 다른 평가 점수를 받을 수 있다.

의미론적 분할(Semantic Segmentation)에서는 일반적으로 각 의미론적 클래스마다 IoU를 독립적으로 계산한다. 동일한 클래스로 예측되고 주석된 픽셀은 교집합을 형성하며, 예측 또는 정답 중 하나라도 해당 클래스에 속하는 모든 픽셀이 합집합을 형성한다. 평균 IoU(mean IoU, mIoU)는 이러한 클래스별 값을 평균하여 평가 데이터셋에 포함된 의미론적 범주 전반의 분할 품질을 간결하게 나타낸다.

분할 지표를 해석할 때는 클래스 불균형(Class Imbalance)을 고려해야 한다. 도로, 바닥, 벽, 배경처럼 넓은 영역이 픽셀 수준 정확도(Pixel-Level Accuracy)를 지배하면 작지만 중요한 객체에 대한 성능이 낮더라도 전체 점수가 높게 나타날 수 있다. 클래스별 IoU와 mIoU는 이러한 문제를 줄이고 개별 범주의 성능을 확인할 수 있도록 하지만, 안전에 중요한 클래스는 전체 평균값 안에 숨기지 않고 별도로 평가해야 한다.

위치추정과 동시적 위치추정 및 지도작성(SLAM)은 출력이 객체 범주가 아니라 궤적(Trajectory) 또는 자세(Pose)이므로 근본적으로 다른 평가 지표를 요구한다. 절대 궤적 오차(Absolute Trajectory Error, ATE)는 적절한 정렬 절차(Alignment Procedure)를 수행한 후 추정된 로봇 궤적과 기준 궤적(Reference Trajectory) 사이의 차이를 평가한다. 따라서 ATE는 누적된 전역 궤적 일관성을 측정하며 비주얼 오도메트리(Visual Odometry), 라이다 오도메트리(LiDAR Odometry), SLAM 시스템 평가에 널리 사용된다.

ATE는 일반적으로 서로 대응하는 추정 자세와 정답 자세 사이의 병진 오차(Translational Error)를 기반으로 계산하며, 평균 제곱근 오차(Root Mean Square Error, RMSE)와 같은 통계값으로 요약할 수 있다. 추정 궤적과 기준 궤적의 좌표계가 서로 다를 수 있기 때문에 비교하기 전에 정렬 과정이 필요할 수 있다. 시스템에 따라 정렬 과정에서 강체 변환(Rigid Transformation)을 보정하며, 단안 카메라 시스템(Monocular System)은 절대 스케일을 직접 관측하기 어려우므로 추가적인 스케일 정렬(Scale Alignment)이 필요할 수 있다.

ATE는 궤적의 길이와 운용 환경을 고려하지 않고 해석해서는 안 된다. 긴 실외 경로에서는 허용 가능한 오차가 정밀한 실내 도킹(Docking)이나 조작 작업에서는 허용되지 않을 수 있다. 또한 두 시스템이 비슷한 최종 ATE 값을 가지더라도 시간에 따른 오차 특성은 크게 다를 수 있다. 따라서 하나의 종합적인 수치뿐 아니라 궤적 그래프, 국부 오차(Local Error), 고장 발생 상황 및 드리프트 패턴(Drift Pattern)을 함께 평가해야 한다.

상대 자세 오차(Relative Pose Error, RPE)는 지정된 시간 또는 거리 구간에서 상대 운동(Relative Motion)의 오차를 측정함으로써 ATE를 보완한다. ATE가 전역적인 궤적 일치도를 강조한다면 RPE는 국부적인 병진 및 회전 드리프트(Translational and Rotational Drift)를 나타낸다. 따라서 위치추정 시스템은 단기적인 운동 오차가 작으면서도 상당한 전역 드리프트를 누적할 수 있으며, 반대로 전역 보정에 의해 최종 ATE는 작더라도 국부적으로 불안정한 추정값을 생성할 수 있다.

추적(Tracking)은 시간에 따라 객체의 식별자(Identity)가 일관되게 유지되어야 하므로 추가적인 평가 기준이 필요하다. 객체 검출 품질만으로는 추적기가 객체의 식별자를 반복적으로 변경하거나 객체를 놓친 후 다시 추적하는 문제를 확인할 수 없다. 따라서 추적 지표는 위치 정확도, 연관(Association), 미검출, 잘못된 추적 및 식별자 일관성을 함께 고려한다. 자율 로봇에서는 경로 계획기가 지속적으로 변화하는 객체 상태를 사용하기 때문에 시간적 안정성(Temporal Stability)이 프레임 단위의 검출 정확도만큼 중요할 수 있다.

평가 지표의 선택은 후속 물리적 작업(Downstream Physical Task)을 반영해야 한다. 높은 mAP가 작은 근거리 장애물을 놓치는 검출기의 안전한 내비게이션을 보장하지 않으며, 높은 mIoU 역시 중요한 경계 영역에서 오류가 발생한다면 유용한 주행 가능성 추정(Traversability Estimation)을 보장하지 않는다. 마찬가지로 낮은 ATE도 위치추정 결과에 짧지만 심각한 자세 점프(Pose Jump)가 존재한다면 안정적인 제어를 보장하지 않는다. 따라서 통계적 점수는 인지 오류가 실제 로봇 동작에 미치는 영향과 연결하여 평가해야 한다.

운용 조건(Operating Condition) 역시 평가 과정에서 명시적으로 반영해야 한다. 성능은 거리, 객체 크기, 속도, 조명, 날씨, 가림(Occlusion), 센서 잡음, 지형 및 환경 복잡도에 따라 변화할 수 있다. 하나의 종합 점수만 보고하면 어려운 조건에서 나타나는 심각한 약점을 숨길 수 있다. 따라서 실제 운용 로봇은 운용 설계 영역(Operational Design Domain)과 예상되는 고장 상황에 대응하는 의미 있는 하위 조건별로 평가 지표를 분석해야 한다.

불확실성(Uncertainty)과 신뢰도 보정(Confidence Calibration)은 또 다른 평가 차원을 제공한다. 이상적인 인지 시스템은 관측이 모호하거나 정상적인 운용 분포를 벗어날수록 낮은 신뢰도를 나타내야 한다. mAP가 약간 낮더라도 신뢰도가 적절하게 보정된 검출기는 잘못된 상황에서도 높은 신뢰도를 유지하는 더 정확한 모델보다 안전한 의사결정을 지원할 수 있다. 따라서 신뢰도 다이어그램(Reliability Diagram)과 보정 오차(Calibration Error)는 기존의 정확도 지표를 보완할 수 있다.

지연시간(Latency) 역시 정확도와 함께 평가해야 한다. 객체 검출기가 매우 높은 mAP를 달성하더라도 움직이는 로봇에서 사용하기에 지나치게 긴 처리시간이 필요할 수 있으며, SLAM 시스템이 정확한 궤적을 생성하더라도 필요한 제어 주기로 자세를 갱신하지 못할 수 있다. 따라서 실제적인 평가는 인지 품질과 함께 종단간 지연시간(End-to-End Latency), 갱신 주기(Update Frequency), 계산 부하, 메모리 사용량 및 목표 배포 하드웨어에서의 시간적 안정성을 함께 보고해야 한다.

벤치마크 결과(Benchmark Result)는 평가 프로토콜(Evaluation Protocol)을 재현할 수 있을 때 가장 유용하다. 데이터셋 버전, 클래스 정의, IoU 임계값, 신뢰도 처리 방법, 궤적 정렬 방식, 좌표계 규칙(Coordinate Convention), 무시 영역(Ignored Region), 평균 계산 방법을 문서화해야 한다. 평가 설정의 작은 차이도 수치 결과에 상당한 차이를 만들 수 있으므로 서로 다른 프로토콜에서 얻은 결과를 직접 비교하면 잘못된 결론을 내릴 수 있다.

따라서 완전한 로봇 인지 평가(Complete Robot Perception Evaluation)는 하나의 대표 점수를 최적화하기보다 서로 보완적인 여러 지표를 함께 사용해야 한다. mAP는 객체 검출 성능을 나타내고, IoU와 mIoU는 공간적 중첩과 분할 품질을 정량화하며, ATE 및 관련 궤적 지표는 위치추정의 일관성을 평가한다. 추적, 신뢰도 보정, 지연시간, 강건성(Robustness), 작업별 안전 지표(Task-Specific Safety Metric)는 이러한 평가를 실제 배포 요구사항으로 확장한다.

인지 평가 지표의 궁극적인 목적은 벤치마크 점수를 최대화하는 것이 아니라 로봇이 신뢰성 있게 행동할 수 있을 만큼 충분한 품질의 정보를 제공받고 있는지를 판단하는 것이다. 의미 있는 평가는 수치 지표를 운용 조건, 시간적 제약, 불확실성 및 물리적 결과와 연결한다. mAP, IoU, ATE 및 보완적인 지표를 함께 해석하면 인지 성능을 자율 로봇 설계를 위한 엔지니어링 근거(Engineering Evidence)로 변환하는 체계적인 평가 프레임워크를 구축할 수 있다.

##  

## 01.07. Open Set and Closed Set Perception Trade offs

![](images/image7.png){width="7.268055555555556in" height="7.268055555555556in"}

Closed-set perception assumes that the categories relevant to a robot are defined before deployment and represented in the training and evaluation process. The system is expected to distinguish among this known set of classes, such as person, vehicle, pallet, rack, door, or forklift. This formulation provides a clear relationship between training labels, model outputs, evaluation metrics, and downstream robot behavior.

The closed-set assumption is attractive for industrial robotics because operational environments are often deliberately constrained. A warehouse AMR may only require reliable recognition of people, pallets, vehicles, infrastructure, and several obstacle categories. Restricting the semantic vocabulary allows training data, decision thresholds, safety rules, and validation procedures to be optimized around a manageable collection of operationally important concepts.

Closed-set models can achieve high accuracy when deployment conditions resemble their training distribution. Their output structure is predictable, computational requirements can be controlled, and every recognized class can be connected to predefined robot behavior. A detected pedestrian may trigger a safety policy, while a recognized charging station can initiate docking. This deterministic semantic interface simplifies integration with planning and control.

The fundamental limitation appears when the robot encounters something outside the predefined vocabulary. Conventional classifiers are generally designed to choose among known categories and may assign an unfamiliar object to the most similar known class. An unusual machine, temporary construction object, animal, or previously unseen vehicle can therefore receive a confident but incorrect label instead of being recognized as unknown.

Open-set perception addresses this problem by assuming that deployment environments can contain categories that were not represented as known classes during training. The system must not only recognize familiar objects but also identify observations that do not fit the known semantic space. This introduces an explicit unknown state and changes perception from pure classification into a combination of recognition and novelty detection.

Detecting unknown objects is difficult because unknown is not a single coherent category. The space of possible unseen objects is effectively unlimited and cannot be represented exhaustively during training. A perception system must infer whether an observation differs sufficiently from its learned classes using confidence, feature-space distance, energy scores, uncertainty estimates, reconstruction behavior, or other measures of distributional familiarity.

Open-set recognition should be distinguished from open-vocabulary perception. Open-set systems primarily ask whether an observation belongs to known classes or should be treated as unknown. Open-vocabulary systems attempt to recognize a much broader and potentially changing collection of concepts, often by comparing visual features with language embeddings. The two approaches are related, but identifying novelty and assigning a meaningful new concept are different problems.

Learned embeddings provide an important mechanism for moving beyond rigid class boundaries. Instead of reducing every observation immediately to one categorical label, a model can preserve a feature vector describing visual, geometric, or multimodal characteristics. New observations can then be compared with known examples or textual concepts. This supports semantic similarity reasoning even when a conventional classifier lacks an explicit output class.

Vision-language models further expand this capability by connecting sensor observations with natural-language descriptions. A robot may compare an observed object with concepts such as "traffic cone," "fallen branch," or "portable barrier" without requiring each term to be represented by a dedicated output neuron during the original classifier design. Such flexibility is particularly valuable for general-purpose robots operating in environments that cannot be completely enumerated beforehand.

Greater semantic flexibility, however, introduces ambiguity. Closed-set classification can be optimized for a small set of operational categories, while open-set and open-vocabulary systems must distinguish among much broader possibilities. Similar objects, unusual viewpoints, poor illumination, occlusion, sensor noise, or domain shift can cause incorrect novelty judgments or misleading semantic matches. Expanded vocabulary therefore does not automatically produce more reliable perception.

False rejection and false acceptance become important trade-offs in open-set systems. If the unknown-detection threshold is too strict, familiar objects may frequently be rejected as unknown, reducing task performance. If it is too permissive, unfamiliar objects can be incorrectly absorbed into known classes. The appropriate threshold depends on the consequences of each error and may differ between semantic interaction, navigation, manipulation, and safety-critical perception.

Safety introduces an important reason for preserving the unknown state. A robot does not always need to know exactly what an unfamiliar object is before responding safely. For navigation, detecting that an unrecognized physical structure occupies the intended path may be sufficient to avoid collision. Geometry and occupancy can therefore provide a safety layer independent of semantic classification, allowing unknown objects to remain physically relevant even when their identity is uncertain.

This separation suggests a hybrid architecture for practical robots. A validated closed-set detector can handle operationally critical classes, geometric perception can detect obstacles regardless of identity, and an open-set or open-vocabulary subsystem can provide broader semantic interpretation. The robot can then use highly validated outputs for safety functions while exploiting flexible recognition for reasoning, interaction, and adaptation.

Confidence calibration becomes especially important in such hybrid systems. A model should not merely produce a class name but also indicate how strongly the observation supports that interpretation. High confidence should correspond to a high probability of correctness, while ambiguous or unfamiliar inputs should reduce confidence. Poorly calibrated models can be particularly dangerous because they may generate convincing semantic labels precisely when the observation lies outside their experience.

Temporal consistency can improve open-set reasoning. A single frame may contain an ambiguous object, but observations accumulated across different viewpoints and distances can provide stronger evidence about whether it belongs to a known category. Tracking preserves object identity while new measurements arrive, allowing semantic hypotheses and uncertainty to evolve over time rather than forcing an irreversible classification from one observation.

Multi-sensor perception provides another source of evidence. An unfamiliar visual appearance may still have clear three-dimensional geometry in LiDAR data, while radar can provide motion information and cameras provide semantic appearance. Combining these modalities can separate questions of existence, location, motion, and identity. A robot can therefore maintain a reliable physical model of an unknown object even when its semantic interpretation remains unresolved.

Evaluation of open-set perception requires more than conventional closed-set accuracy or mAP. The system must be evaluated on its ability to preserve known-class performance while detecting unknown observations. Test datasets should therefore contain both familiar and unfamiliar categories, and evaluation should examine incorrect acceptance of unknowns, rejection of known objects, confidence behavior, and the effect of novelty detection on downstream robot decisions.

The operational design domain strongly influences the appropriate balance. A highly structured manufacturing cell may favor closed-set perception because the environment, objects, and tasks are tightly controlled and extensive validation is possible. An outdoor service robot, humanoid, or general-purpose Physical AI system encounters much greater environmental diversity and therefore benefits more strongly from open-set and open-vocabulary capabilities.

Computational cost also differs between approaches. A compact closed-set detector can provide predictable low-latency inference on edge hardware, while large multimodal embedding models may require substantially greater memory and computation. Broad semantic reasoning may therefore operate at a lower frequency than safety-critical perception. Multi-rate architectures allow fast geometric and closed-set processing to coexist with slower but richer open-world interpretation.

The central engineering trade-off is consequently between specialization and flexibility. Closed-set perception offers controlled semantics, efficient computation, straightforward validation, and strong performance within known conditions. Open-set perception adds explicit handling of novelty, while open-vocabulary methods extend recognition toward previously unspecified concepts. Each increase in flexibility introduces additional uncertainty, computational cost, and validation complexity.

For Physical AI, the most practical direction is not necessarily to replace closed-set perception with completely unrestricted recognition. Robots can maintain trusted perception pathways for safety and mission-critical categories while using embeddings, novelty detection, language models, and semantic graphs to interpret unfamiliar situations. This layered design separates the requirement to remain physically safe from the ambition to understand an increasingly open world.

Open-set and closed-set perception should therefore be viewed as complementary design strategies rather than competing endpoints. The appropriate architecture depends on environmental variability, task criticality, available computation, validation requirements, and acceptable risk. Robust autonomous robots combine predictable recognition of known operational concepts with mechanisms for detecting, representing, and cautiously reasoning about what they have never encountered before.

폐쇄형 집합 인지(Closed-Set Perception)는 로봇과 관련된 범주가 배포 전에 정의되고 학습 및 평가 과정에 포함되어 있다고 가정한다. 시스템은 사람, 차량, 팔레트, 랙(Rack), 문, 지게차와 같이 사전에 알려진 클래스 집합을 구분하도록 설계된다. 이러한 구성은 학습 레이블(Training Label), 모델 출력(Model Output), 평가 지표(Evaluation Metric), 후속 로봇 행동(Downstream Robot Behavior) 사이에 명확한 관계를 제공한다.

폐쇄형 집합 가정(Closed-Set Assumption)은 운용 환경이 의도적으로 제한되는 경우가 많은 산업용 로보틱스(Industrial Robotics)에서 특히 유용하다. 창고 자율이동로봇(AMR)은 사람, 팔레트, 차량, 인프라 및 몇 가지 장애물 범주만 안정적으로 인식하면 충분할 수 있다. 의미론적 어휘(Semantic Vocabulary)를 제한하면 관리 가능한 운용 핵심 개념을 중심으로 학습 데이터, 판단 임계값, 안전 규칙 및 검증 절차를 최적화할 수 있다.

폐쇄형 집합 모델은 실제 운용 조건이 학습 분포(Training Distribution)와 유사할 때 높은 정확도를 달성할 수 있다. 출력 구조를 예측할 수 있고 계산 요구량을 통제할 수 있으며, 인식된 각 클래스를 사전에 정의된 로봇 행동과 연결할 수 있다. 예를 들어 보행자가 검출되면 안전 정책(Safety Policy)을 실행하고, 충전 스테이션이 인식되면 도킹(Docking)을 시작할 수 있다. 이러한 결정론적 의미론 인터페이스(Deterministic Semantic Interface)는 경로 계획 및 제어와의 통합을 단순화한다.

근본적인 한계는 로봇이 사전에 정의된 어휘에 포함되지 않은 대상을 만날 때 나타난다. 기존 분류기(Classifier)는 일반적으로 알려진 범주 중 하나를 선택하도록 설계되므로 익숙하지 않은 객체를 가장 유사한 알려진 클래스로 분류할 수 있다. 따라서 특이한 기계, 임시 공사 구조물, 동물 또는 이전에 관측하지 못한 차량이 미확인 객체(Unknown Object)로 인식되지 않고 높은 신뢰도로 잘못된 레이블을 받을 수 있다.

개방형 집합 인지(Open-Set Perception)는 실제 운용 환경에 학습 과정에서 알려진 클래스로 포함되지 않았던 범주가 존재할 수 있다고 가정함으로써 이러한 문제를 다룬다. 시스템은 익숙한 객체를 인식하는 것뿐만 아니라 알려진 의미 공간(Known Semantic Space)에 적합하지 않은 관측도 식별해야 한다. 이에 따라 명시적인 미확인 상태(Unknown State)가 도입되며, 인지는 단순한 분류에서 인식과 신규성 검출(Novelty Detection)을 결합한 문제로 확장된다.

미확인 객체를 검출하는 것은 미확인이라는 개념 자체가 하나의 일관된 범주가 아니기 때문에 어렵다. 가능한 미관측 객체의 공간은 사실상 무한하며 학습 과정에서 이를 모두 표현할 수 없다. 따라서 인지 시스템은 신뢰도, 특징 공간 거리(Feature-Space Distance), 에너지 점수(Energy Score), 불확실성 추정(Uncertainty Estimation), 재구성 특성(Reconstruction Behavior) 또는 분포 친숙도(Distributional Familiarity)를 나타내는 다른 척도를 이용하여 관측이 학습된 클래스와 충분히 다른지를 추론해야 한다.

개방형 집합 인식(Open-Set Recognition)은 개방형 어휘 인지(Open-Vocabulary Perception)와 구분해야 한다. 개방형 집합 시스템은 주로 관측 대상이 알려진 클래스에 속하는지 아니면 미확인 대상으로 처리해야 하는지를 판단한다. 반면 개방형 어휘 시스템은 시각적 특징을 언어 임베딩(Language Embedding)과 비교하는 등의 방법을 통해 훨씬 광범위하고 변화 가능한 개념 집합을 인식하려 한다. 두 접근법은 관련되어 있지만 신규성을 식별하는 것과 새로운 대상에 의미 있는 개념을 부여하는 것은 서로 다른 문제이다.

학습된 임베딩(Learned Embedding)은 경직된 클래스 경계를 넘어가기 위한 중요한 방법을 제공한다. 모든 관측을 즉시 하나의 범주형 레이블로 축소하는 대신, 모델은 시각적·기하학적 또는 다중모달 특성(Multimodal Characteristic)을 설명하는 특징 벡터(Feature Vector)를 유지할 수 있다. 새로운 관측은 알려진 예제나 텍스트 개념과 비교될 수 있으며, 이를 통해 기존 분류기에 명시적인 출력 클래스가 존재하지 않더라도 의미론적 유사성 추론(Semantic Similarity Reasoning)을 수행할 수 있다.

비전-언어 모델(Vision-Language Model)은 센서 관측과 자연어 설명을 연결함으로써 이러한 능력을 더욱 확장한다. 로봇은 기존 분류기 설계 단계에서 각각의 용어에 전용 출력 뉴런(Output Neuron)을 정의하지 않았더라도 관측된 객체를 "교통 콘(Traffic Cone)", "쓰러진 나뭇가지(Fallen Branch)", "이동식 차단막(Portable Barrier)"과 같은 개념과 비교할 수 있다. 이러한 유연성은 사전에 모든 환경 요소를 완전히 열거할 수 없는 환경에서 동작하는 범용 로봇에 특히 유용하다.

그러나 의미론적 유연성(Semantic Flexibility)이 증가하면 모호성(Ambiguity)도 함께 증가한다. 폐쇄형 집합 분류는 소수의 운용 범주에 맞춰 최적화할 수 있지만, 개방형 집합 및 개방형 어휘 시스템은 훨씬 광범위한 가능성을 구분해야 한다. 서로 유사한 객체, 특이한 관측 시점, 낮은 조도, 가림(Occlusion), 센서 잡음 또는 도메인 변화(Domain Shift)는 잘못된 신규성 판단이나 부정확한 의미론적 대응을 발생시킬 수 있다. 따라서 어휘 범위를 확장한다고 해서 인지의 신뢰성이 자동으로 향상되는 것은 아니다.

거짓 거부(False Rejection)와 거짓 수용(False Acceptance)은 개방형 집합 시스템에서 중요한 절충 관계를 형성한다. 미확인 검출 임계값이 지나치게 엄격하면 익숙한 객체까지 미확인 대상으로 자주 거부되어 작업 성능이 저하될 수 있다. 반대로 지나치게 관대하면 익숙하지 않은 객체가 알려진 클래스에 잘못 포함될 수 있다. 적절한 임계값은 각 오류가 초래하는 결과에 따라 달라지며 의미론적 상호작용, 내비게이션, 조작 및 안전 중요 인지(Safety-Critical Perception)에서 서로 다르게 설정될 수 있다.

안전 측면에서는 미확인 상태를 유지해야 할 중요한 이유가 존재한다. 로봇은 익숙하지 않은 객체에 안전하게 대응하기 전에 그것이 정확히 무엇인지 반드시 알아야 하는 것은 아니다. 내비게이션에서는 인식되지 않은 물리적 구조물이 예정된 경로를 점유하고 있다는 사실만 검출해도 충돌을 피하는 데 충분할 수 있다. 따라서 기하학(Geometry)과 점유 정보(Occupancy)는 의미론적 분류와 독립적인 안전 계층(Safety Layer)을 제공하여 객체의 정체성이 불확실하더라도 미확인 객체를 물리적으로 중요한 대상으로 유지할 수 있다.

이러한 분리는 실제 로봇을 위한 하이브리드 아키텍처(Hybrid Architecture)를 제안한다. 검증된 폐쇄형 집합 검출기(Closed-Set Detector)는 운용상 중요한 클래스를 처리하고, 기하학적 인지(Geometric Perception)는 객체의 정체성과 관계없이 장애물을 검출하며, 개방형 집합 또는 개방형 어휘 하위 시스템은 보다 광범위한 의미론적 해석을 제공할 수 있다. 이를 통해 로봇은 안전 기능에는 충분히 검증된 출력을 사용하면서 추론, 상호작용 및 적응에는 유연한 인식 능력을 활용할 수 있다.

이러한 하이브리드 시스템에서는 신뢰도 보정(Confidence Calibration)이 특히 중요하다. 모델은 단순히 클래스 이름을 출력하는 데 그치지 않고 관측이 해당 해석을 얼마나 강하게 지지하는지도 나타내야 한다. 높은 신뢰도는 높은 정답 확률과 대응해야 하며, 모호하거나 익숙하지 않은 입력에서는 신뢰도가 낮아져야 한다. 신뢰도 보정이 잘못된 모델은 경험 범위를 벗어난 관측에 대해서도 확신에 찬 의미론적 레이블을 생성할 수 있기 때문에 특히 위험할 수 있다.

시간적 일관성(Temporal Consistency)은 개방형 집합 추론을 향상시킬 수 있다. 하나의 프레임에서는 객체가 모호하게 보일 수 있지만 서로 다른 관측 각도와 거리에서 누적된 관측은 해당 객체가 알려진 범주에 속하는지 판단하는 데 더 강한 근거를 제공할 수 있다. 추적(Tracking)은 새로운 측정값이 들어오는 동안 객체의 식별자를 유지하여 하나의 관측으로 되돌릴 수 없는 분류를 즉시 결정하는 대신 시간에 따라 의미론적 가설과 불확실성을 갱신할 수 있도록 한다.

다중 센서 인지(Multi-Sensor Perception)는 또 다른 판단 근거를 제공한다. 시각적으로 익숙하지 않은 외형을 가진 객체도 라이다 데이터에서는 명확한 3차원 기하학을 가질 수 있으며, 레이더는 운동 정보를 제공하고 카메라는 의미론적 외형 정보를 제공할 수 있다. 이러한 모달리티를 결합하면 존재, 위치, 운동 및 정체성에 대한 문제를 서로 분리할 수 있다. 따라서 로봇은 의미론적 해석이 해결되지 않은 상태에서도 미확인 객체에 대한 신뢰할 수 있는 물리적 모델을 유지할 수 있다.

개방형 집합 인지의 평가는 기존의 폐쇄형 집합 정확도나 mAP만으로는 충분하지 않다. 시스템은 알려진 클래스에 대한 성능을 유지하면서 미확인 관측을 검출하는 능력을 함께 평가해야 한다. 따라서 시험 데이터셋에는 익숙한 범주와 익숙하지 않은 범주가 모두 포함되어야 하며, 평가 과정에서는 미확인 객체의 잘못된 수용, 알려진 객체의 잘못된 거부, 신뢰도 특성 및 신규성 검출이 후속 로봇 판단에 미치는 영향을 함께 분석해야 한다.

운용 설계 영역(Operational Design Domain)은 적절한 균형을 결정하는 데 큰 영향을 미친다. 고도로 구조화된 제조 셀(Manufacturing Cell)은 환경, 객체 및 작업이 엄격하게 통제되고 광범위한 검증이 가능하기 때문에 폐쇄형 집합 인지가 유리할 수 있다. 반면 실외 서비스 로봇, 휴머노이드(Humanoid), 범용 피지컬 AI(General-Purpose Physical AI) 시스템은 훨씬 다양한 환경을 경험하므로 개방형 집합 및 개방형 어휘 능력에서 더 큰 이점을 얻을 수 있다.

계산 비용(Computational Cost) 역시 접근법에 따라 달라진다. 소형 폐쇄형 집합 검출기는 엣지 하드웨어(Edge Hardware)에서 예측 가능한 저지연 추론을 제공할 수 있지만, 대규모 다중모달 임베딩 모델(Multimodal Embedding Model)은 훨씬 많은 메모리와 계산 자원을 요구할 수 있다. 따라서 광범위한 의미론적 추론은 안전 중요 인지보다 낮은 주기로 동작할 수 있다. 다중 주기 아키텍처(Multi-Rate Architecture)는 빠른 기하학적·폐쇄형 집합 처리와 느리지만 풍부한 개방형 환경 해석을 함께 운용할 수 있도록 한다.

결과적으로 핵심적인 엔지니어링 절충 관계(Engineering Trade-Off)는 전문화(Specialization)와 유연성(Flexibility) 사이에 존재한다. 폐쇄형 집합 인지는 통제된 의미 체계, 효율적인 계산, 명확한 검증, 알려진 조건에서의 높은 성능을 제공한다. 개방형 집합 인지는 신규성을 명시적으로 처리하는 기능을 추가하며, 개방형 어휘 방식은 이전에 지정되지 않았던 개념까지 인식 범위를 확장한다. 그러나 유연성이 증가할수록 추가적인 불확실성, 계산 비용 및 검증 복잡성도 함께 증가한다.

피지컬 AI(Physical AI)에서 가장 실용적인 방향은 폐쇄형 집합 인지를 완전히 제한 없는 인식으로 대체하는 것만을 의미하지 않는다. 로봇은 안전 및 임무 중요 범주(Mission-Critical Category)를 위한 신뢰 가능한 인지 경로를 유지하면서 임베딩, 신규성 검출, 언어 모델(Language Model), 의미론적 그래프(Semantic Graph)를 활용하여 익숙하지 않은 상황을 해석할 수 있다. 이러한 계층형 설계(Layered Design)는 물리적으로 안전해야 한다는 요구와 점점 더 개방된 세계를 이해하려는 목표를 분리한다.

따라서 개방형 집합 인지와 폐쇄형 집합 인지는 서로 경쟁하는 최종 선택지가 아니라 상호 보완적인 설계 전략(Complementary Design Strategy)으로 이해해야 한다. 적절한 아키텍처는 환경의 다양성, 작업 중요도, 사용 가능한 계산 자원, 검증 요구사항 및 허용 가능한 위험 수준에 따라 결정된다. 강건한 자율 로봇(Robust Autonomous Robot)은 알려진 운용 개념을 예측 가능하게 인식하는 능력과 이전에 경험하지 못한 대상을 검출하고 표현하며 신중하게 추론하는 메커니즘을 함께 결합한다.

##  

## 01.08. Perception Safety Requirements and Fail Safe

![](images/image8.png){width="7.268055555555556in" height="7.268055555555556in"}

Perception safety begins with the principle that a robot must never assume its interpretation of the environment is perfectly correct. Cameras, LiDAR, radar, IMUs, learned models, and fusion algorithms can all fail or become unreliable. A safety-oriented perception architecture therefore treats uncertainty, missing observations, degraded sensors, timing violations, and inconsistent outputs as expected operating conditions that must be detected and managed.

Safety requirements should be derived from the physical consequences of perception failure rather than from perception accuracy alone. Missing a pedestrian, incorrectly estimating obstacle distance, losing localization, or using stale sensor information can directly influence motion decisions. Requirements must therefore connect perception performance with robot speed, stopping distance, operating environment, control response, and the severity of possible collisions or unsafe interactions.

A fundamental requirement is sufficient sensing coverage around the robot. Safety-relevant regions must remain observable across the intended operational design domain, including near-field areas, stopping corridors, turning regions, and manipulation workspaces. Sensor placement should minimize blind zones while considering occlusion, contamination, vibration, lighting, weather, reflective surfaces, and mechanical structures that may obstruct measurements.

Redundancy can reduce dependence on a single sensing mechanism. Cameras provide rich appearance information but can degrade under poor illumination, while LiDAR provides direct geometric measurements but can suffer from occlusion or adverse surface properties. Radar can remain useful in conditions that challenge optical sensors. Combining heterogeneous sensing principles allows one modality to preserve partial environmental awareness when another becomes degraded.

Redundancy alone does not guarantee safety because multiple sensors can fail for a common reason. Heavy contamination may obstruct several externally mounted sensors, incorrect calibration can affect an entire fusion pipeline, and power or communication faults can simultaneously remove multiple data sources. Safety analysis must therefore distinguish independent redundancy from common-cause failure and identify dependencies that could invalidate apparently redundant perception channels.

Sensor health monitoring provides the first layer of failure detection. A perception system can monitor frame rates, timestamps, packet loss, signal quality, image brightness, LiDAR point density, radar status, temperature, synchronization, communication state, and internal diagnostic messages. Measurements that violate expected ranges should be marked as degraded or invalid rather than silently propagated into downstream fusion and planning.

Data freshness is also a safety property. A technically correct detection can become dangerous if it represents the environment too far in the past. Each safety-relevant observation should retain an acquisition timestamp, and the system should monitor end-to-end age through preprocessing, inference, fusion, tracking, and communication. Outputs exceeding defined freshness limits can be rejected or cause the robot to enter a more conservative operating state.

Perception confidence should express more than classification probability. Safety decisions may depend on object existence confidence, localization covariance, depth uncertainty, tracking stability, sensor validity, temporal consistency, and agreement among independent observations. These indicators allow downstream modules to distinguish a strongly supported environmental estimate from a weak hypothesis instead of treating every perception output as equally trustworthy.

Cross-checking between independent representations can expose failures that one processing path cannot detect internally. A semantic detector may report free space while geometric occupancy indicates a physical obstacle, or camera-based localization may disagree with LiDAR odometry. Such inconsistencies should not automatically be resolved by trusting one source. They can instead trigger uncertainty escalation, additional verification, speed reduction, or a degraded operating mode.

Safety-critical obstacle perception should avoid depending entirely on semantic recognition. A robot does not need to know whether an unexpected object is a box, animal, machine part, or unfamiliar vehicle before avoiding collision. Geometry-based obstacle detection, occupancy estimation, range thresholds, and protective fields can provide an independent physical safety path while higher-level semantic perception determines object identity and task relevance.

Fail-safe behavior defines what the robot does when perception can no longer support normal autonomous operation. The safest response is not always an immediate emergency stop. Depending on the failure, environment, and robot dynamics, the system may reduce speed, increase following distance, restrict maneuvering, disable autonomous functions, request remote assistance, move toward a safe location, or execute a controlled stop.

A degraded mode should correspond to the remaining perception capability. If one camera fails while LiDAR and radar remain healthy, limited navigation may still be possible. If localization uncertainty increases, the robot may reduce speed and restrict operation to geometrically simple areas. If forward obstacle sensing becomes unavailable, continued forward motion may no longer be acceptable. Degradation policies should therefore be linked explicitly to specific failure combinations.

Fail-safe and fail-operational behavior represent different design objectives. A fail-safe system transitions toward a condition that minimizes risk after a critical failure, commonly by stopping or disabling motion. A fail-operational system maintains some useful functionality despite failures through redundancy and reconfiguration. Mobile robots often require a combination, remaining operational after tolerable faults while transitioning to a safe state when minimum sensing capability is lost.

Emergency stopping should remain architecturally independent from complex learned perception whenever practical. Dedicated safety sensors, protective scanners, bumpers, emergency-stop circuits, or independently validated geometric checks can provide a final protective layer. This architectural diversity reduces the possibility that one software defect, neural-network failure, corrupted model, or computational overload simultaneously disables both intelligent perception and emergency protection.

Machine-learning perception introduces additional safety challenges because failure boundaries are difficult to specify analytically. Performance can change with weather, illumination, unusual objects, sensor aging, environmental appearance, and distribution shift. Validation must therefore cover not only average accuracy but also difficult conditions, rare scenarios, confidence behavior, out-of-distribution inputs, and combinations of factors likely to occur during the robot\'s operational lifetime.

Runtime monitoring can complement offline validation by detecting when current conditions differ from those expected during development. Unusual feature distributions, low confidence, disagreement between models, persistent tracking instability, abnormal sensor statistics, or unexpected environmental conditions can indicate reduced perception reliability. Runtime monitors do not need to identify the exact failure cause before requesting more conservative robot behavior.

Localization safety requires particular attention because localization errors affect every spatial interpretation expressed in the robot or map frame. Sudden pose jumps, excessive covariance, inconsistent odometry, GNSS degradation, map-matching failure, or transform discontinuities can make otherwise accurate obstacle detections appear in incorrect locations. Plausibility checks on position, velocity, acceleration, orientation, and cross-sensor consistency help detect these failures.

Calibration is similarly safety relevant. Camera-to-LiDAR, sensor-to-body, and other extrinsic transformations can change because of vibration, impact, maintenance, or mechanical deformation. Incorrect calibration may produce apparently valid sensor measurements that become incorrect when fused. Systems should therefore verify calibration during commissioning and maintenance and, where feasible, monitor alignment consistency during operation.

Resource failures must also be considered part of perception safety. GPU overload, memory exhaustion, thermal throttling, processor contention, middleware congestion, and excessive logging can increase latency or prevent perception updates. Watchdogs and resource monitors should supervise update rates, execution deadlines, queue depth, memory usage, accelerator health, and process status so computational degradation becomes visible before it produces uncontrolled stale perception.

The interface between perception and planning should explicitly communicate validity. Downstream modules should receive timestamps, confidence or uncertainty, health state, and degradation information together with environmental estimates. A planner should not continue normal operation merely because a message exists. It should verify that the information is recent, sufficiently reliable, and supported by the minimum sensing configuration required for the current maneuver.

Safety validation should include deliberate fault injection rather than only nominal testing. Engineers can disconnect sensors, delay messages, corrupt timestamps, introduce calibration errors, obscure cameras, reduce LiDAR returns, overload processors, or create inconsistent sensor observations. The objective is to confirm that failures are detected within the required time and that the robot transitions predictably to the intended degraded or safe state.

Perception safety is therefore achieved through layers rather than through a single highly accurate model. Sensor diversity, health monitoring, uncertainty estimation, geometric safeguards, temporal validation, cross-checking, runtime supervision, degraded operation, and independent emergency protection collectively reduce risk. Each layer addresses failure modes that other layers may not detect, creating defense in depth across the complete perception architecture.

For Physical AI systems, increasingly capable learned perception should strengthen rather than replace this layered safety structure. Foundation models, open-vocabulary perception, temporal reasoning, and multimodal representations can improve environmental understanding, but their outputs should remain bounded by explicit validity checks and physical safety mechanisms. Intelligence can expand what the robot understands while safety architecture constrains what it is permitted to do.

A production perception system should ultimately be evaluated not only by how well it performs when everything works, but by how predictably it behaves when something does not. Safe perception requires knowing when information is valid, detecting when confidence has degraded, preserving independent protection against critical hazards, and selecting an appropriate fallback response. Fail-safe perception converts unavoidable uncertainty and failure into controlled robot behavior rather than uncontrolled physical risk.

인지 안전(Perception Safety)은 로봇이 환경에 대한 자신의 해석이 완벽하게 정확하다고 절대로 가정해서는 안 된다는 원칙에서 시작한다. 카메라, 라이다(LiDAR), 레이더(Radar), 관성측정장치(IMU), 학습 모델(Learned Model), 융합 알고리즘(Fusion Algorithm)은 모두 고장 나거나 신뢰성이 저하될 수 있다. 따라서 안전 중심 인지 아키텍처(Safety-Oriented Perception Architecture)는 불확실성, 관측 누락, 센서 성능 저하, 시간 제약 위반, 출력 불일치를 검출하고 관리해야 하는 정상적인 운용 조건으로 취급한다.

안전 요구사항(Safety Requirement)은 인지 정확도만을 기준으로 하는 것이 아니라 인지 실패가 초래할 수 있는 물리적 결과에서 도출해야 한다. 보행자를 놓치거나, 장애물 거리를 잘못 추정하거나, 위치추정(Localization)을 상실하거나, 오래된 센서 정보를 사용하는 것은 로봇의 움직임 결정에 직접적인 영향을 줄 수 있다. 따라서 요구사항은 인지 성능을 로봇 속도, 정지 거리, 운용 환경, 제어 응답 및 충돌이나 위험한 상호작용으로 발생할 수 있는 피해의 심각도와 연결해야 한다.

기본적인 요구사항 중 하나는 로봇 주변에 충분한 센싱 범위(Sensing Coverage)를 확보하는 것이다. 근거리 영역, 정지 경로(Stopping Corridor), 회전 영역 및 조작 작업공간(Manipulation Workspace)을 포함하여 의도된 운용 설계 영역(Operational Design Domain)에서 안전과 관련된 영역을 지속적으로 관측할 수 있어야 한다. 센서 배치는 가림, 오염, 진동, 조명, 날씨, 반사 표면 및 측정을 방해할 수 있는 기계 구조물을 고려하면서 사각지대(Blind Zone)를 최소화해야 한다.

중복성(Redundancy)은 하나의 센싱 방식에 대한 의존성을 줄일 수 있다. 카메라는 풍부한 외형 정보를 제공하지만 조명이 좋지 않은 환경에서는 성능이 저하될 수 있으며, 라이다는 직접적인 기하학 측정을 제공하지만 가림이나 불리한 표면 특성의 영향을 받을 수 있다. 레이더는 광학 센서가 어려움을 겪는 조건에서도 유용할 수 있다. 서로 다른 센싱 원리를 결합하면 하나의 모달리티(Modality)가 성능 저하를 겪더라도 다른 모달리티가 환경 인식 능력의 일부를 유지할 수 있다.

그러나 중복성 자체가 안전을 보장하지는 않는다. 여러 센서가 동일한 원인으로 동시에 고장 날 수 있기 때문이다. 심한 오염은 외부에 장착된 여러 센서를 동시에 방해할 수 있고, 잘못된 보정(Calibration)은 전체 융합 파이프라인에 영향을 줄 수 있으며, 전원 또는 통신 고장은 여러 데이터 소스를 동시에 상실하게 만들 수 있다. 따라서 안전 분석은 독립적 중복성(Independent Redundancy)과 공통 원인 고장(Common-Cause Failure)을 구분하고, 겉보기에는 중복된 인지 채널을 동시에 무력화할 수 있는 의존성을 식별해야 한다.

센서 상태 모니터링(Sensor Health Monitoring)은 고장 검출의 첫 번째 계층을 제공한다. 인지 시스템은 프레임률(Frame Rate), 타임스탬프(Timestamp), 패킷 손실(Packet Loss), 신호 품질, 영상 밝기, 라이다 포인트 밀도, 레이더 상태, 온도, 동기화, 통신 상태 및 내부 진단 메시지를 모니터링할 수 있다. 예상 범위를 벗어난 측정값은 후속 융합 및 경로 계획으로 그대로 전달하지 않고 성능 저하(Degraded) 또는 무효(Invalid) 상태로 표시해야 한다.

데이터 최신성(Data Freshness) 역시 안전 특성이다. 기술적으로 정확한 검출 결과라도 지나치게 오래된 환경을 나타낸다면 위험할 수 있다. 안전과 관련된 각각의 관측값에는 획득 타임스탬프(Acquisition Timestamp)를 유지해야 하며, 시스템은 전처리, 추론, 융합, 추적 및 통신 전체 과정에서 종단간 정보 수명(End-to-End Age)을 모니터링해야 한다. 정의된 최신성 한계(Freshness Limit)를 초과한 출력은 거부하거나 로봇이 더욱 보수적인 운용 상태로 전환하도록 해야 한다.

인지 신뢰도(Perception Confidence)는 단순한 분류 확률 이상의 정보를 표현해야 한다. 안전 관련 판단은 객체 존재 신뢰도(Object Existence Confidence), 위치추정 공분산(Localization Covariance), 깊이 불확실성(Depth Uncertainty), 추적 안정성(Tracking Stability), 센서 유효성, 시간적 일관성(Temporal Consistency), 독립적인 관측 사이의 일치도 등에 의존할 수 있다. 이러한 지표를 이용하면 후속 모듈은 모든 인지 출력을 동일하게 신뢰하는 대신 강하게 뒷받침된 환경 추정과 불확실한 가설을 구분할 수 있다.

독립적인 표현 사이의 교차 검증(Cross-Checking)은 하나의 처리 경로 내부에서는 검출하기 어려운 고장을 발견할 수 있다. 의미론적 검출기(Semantic Detector)가 자유 공간(Free Space)을 보고하지만 기하학적 점유 정보(Geometric Occupancy)가 물리적 장애물을 나타낼 수 있으며, 카메라 기반 위치추정이 라이다 오도메트리(LiDAR Odometry)와 일치하지 않을 수도 있다. 이러한 불일치는 하나의 정보원을 자동으로 신뢰하여 해결하기보다 불확실성을 높이고, 추가 검증을 수행하거나, 속도를 줄이거나, 성능 저하 운용 모드(Degraded Operating Mode)로 전환하는 계기로 활용할 수 있다.

안전 중요 장애물 인지(Safety-Critical Obstacle Perception)는 의미론적 인식에 전적으로 의존해서는 안 된다. 로봇은 예상하지 못한 객체와 충돌을 피하기 전에 그것이 상자, 동물, 기계 부품 또는 익숙하지 않은 차량인지 정확하게 알아야 할 필요는 없다. 기하학 기반 장애물 검출(Geometry-Based Obstacle Detection), 점유 추정(Occupancy Estimation), 거리 임계값 및 보호 영역(Protective Field)은 독립적인 물리적 안전 경로를 제공할 수 있으며, 상위 수준 의미론적 인지는 객체의 정체성과 작업 관련성을 판단할 수 있다.

고장 안전 동작(Fail-Safe Behavior)은 인지 시스템이 더 이상 정상적인 자율 운용을 지원할 수 없을 때 로봇이 무엇을 해야 하는지를 정의한다. 가장 안전한 대응이 항상 즉각적인 비상 정지(Emergency Stop)를 의미하는 것은 아니다. 고장 유형, 환경 및 로봇 동역학에 따라 시스템은 속도를 낮추거나, 추종 거리를 증가시키거나, 기동을 제한하거나, 자율 기능을 비활성화하거나, 원격 지원을 요청하거나, 안전한 위치로 이동하거나, 제어된 정지(Controlled Stop)를 수행할 수 있다.

성능 저하 모드(Degraded Mode)는 남아 있는 인지 능력에 대응해야 한다. 하나의 카메라가 고장 나더라도 라이다와 레이더가 정상이라면 제한적인 내비게이션을 계속 수행할 수 있다. 위치추정 불확실성이 증가하면 로봇은 속도를 낮추고 기하학적으로 단순한 영역으로 운용을 제한할 수 있다. 전방 장애물 센싱을 사용할 수 없다면 전진 동작을 계속 허용할 수 없을 수 있다. 따라서 성능 저하 정책(Degradation Policy)은 구체적인 고장 조합과 명시적으로 연결되어야 한다.

고장 안전(Fail-Safe)과 고장 운용 지속(Fail-Operational)은 서로 다른 설계 목표를 나타낸다. 고장 안전 시스템은 심각한 고장이 발생하면 일반적으로 정지하거나 움직임을 비활성화하여 위험을 최소화하는 상태로 전환한다. 고장 운용 지속 시스템은 중복성과 재구성(Reconfiguration)을 이용하여 고장이 발생한 상태에서도 일부 유용한 기능을 유지한다. 이동 로봇은 일반적으로 두 방식을 결합하여 허용 가능한 고장에서는 운용을 지속하고 최소 센싱 능력을 상실하면 안전 상태(Safe State)로 전환해야 한다.

가능하다면 비상 정지는 복잡한 학습 기반 인지(Learned Perception)와 아키텍처적으로 독립된 상태를 유지해야 한다. 전용 안전 센서(Dedicated Safety Sensor), 보호용 스캐너(Protective Scanner), 범퍼(Bumper), 비상 정지 회로 또는 독립적으로 검증된 기하학적 검사를 통해 최종적인 보호 계층을 구성할 수 있다. 이러한 아키텍처 다양성(Architectural Diversity)은 하나의 소프트웨어 결함, 신경망 고장, 손상된 모델 또는 계산 과부하가 지능형 인지와 비상 보호 기능을 동시에 무력화할 가능성을 줄인다.

머신러닝 인지(Machine-Learning Perception)는 고장 경계를 분석적으로 명확하게 정의하기 어렵기 때문에 추가적인 안전 문제를 발생시킨다. 날씨, 조명, 특이한 객체, 센서 노화, 환경 외형 및 분포 변화(Distribution Shift)에 따라 성능이 달라질 수 있다. 따라서 검증(Validation)은 평균 정확도뿐 아니라 어려운 조건, 희귀 상황, 신뢰도 특성, 분포 외 입력(Out-of-Distribution Input), 로봇의 운용 수명 동안 발생할 가능성이 있는 여러 조건의 조합까지 포함해야 한다.

런타임 모니터링(Runtime Monitoring)은 현재 운용 조건이 개발 단계에서 예상했던 조건과 달라지는 상황을 검출함으로써 오프라인 검증을 보완할 수 있다. 비정상적인 특징 분포, 낮은 신뢰도, 모델 사이의 불일치, 지속적인 추적 불안정성, 비정상적인 센서 통계 또는 예상하지 못한 환경 조건은 인지 신뢰성 저하를 나타낼 수 있다. 런타임 모니터가 정확한 고장 원인을 식별하지 못하더라도 보다 보수적인 로봇 동작을 요구할 수 있다.

위치추정 안전(Localization Safety)은 위치추정 오류가 로봇 좌표계 또는 지도 좌표계로 표현되는 모든 공간 해석에 영향을 주기 때문에 특별한 주의가 필요하다. 갑작스러운 자세 점프(Pose Jump), 과도한 공분산, 일관되지 않은 오도메트리, GNSS 성능 저하, 지도 정합(Map Matching) 실패 또는 좌표 변환 불연속(Transform Discontinuity)은 정확한 장애물 검출 결과조차 잘못된 위치에 존재하는 것처럼 만들 수 있다. 위치, 속도, 가속도, 자세 및 센서 간 일관성에 대한 타당성 검사(Plausibility Check)는 이러한 고장을 검출하는 데 도움을 준다.

보정(Calibration) 역시 안전과 직접적으로 관련된다. 카메라-라이다(Camera-to-LiDAR), 센서-차체(Sensor-to-Body) 및 기타 외부 파라미터 변환(Extrinsic Transformation)은 진동, 충격, 유지보수 또는 기계적 변형으로 인해 달라질 수 있다. 잘못된 보정은 개별적으로는 정상적으로 보이는 센서 측정값을 융합 과정에서 잘못된 정보로 만들 수 있다. 따라서 시스템은 초기 설치 및 유지보수 과정에서 보정 상태를 검증해야 하며, 가능한 경우 운용 중에도 정렬 일관성(Alignment Consistency)을 모니터링해야 한다.

컴퓨팅 자원 고장(Resource Failure)도 인지 안전의 일부로 고려해야 한다. GPU 과부하, 메모리 고갈, 열 스로틀링(Thermal Throttling), 프로세서 자원 경쟁, 미들웨어 혼잡 및 과도한 로깅은 지연시간을 증가시키거나 인지 갱신을 중단시킬 수 있다. 감시 장치(Watchdog)와 자원 모니터(Resource Monitor)는 갱신 주기, 실행 마감시간(Execution Deadline), 큐 깊이, 메모리 사용량, 가속기 상태 및 프로세스 상태를 감시하여 계산 성능 저하가 통제되지 않은 오래된 인지 정보로 이어지기 전에 문제를 가시화해야 한다.

인지와 경로 계획 사이의 인터페이스는 정보의 유효성(Validity)을 명시적으로 전달해야 한다. 후속 모듈은 환경 추정값과 함께 타임스탬프, 신뢰도 또는 불확실성, 상태 정보(Health State), 성능 저하 정보를 전달받아야 한다. 경로 계획기는 단순히 메시지가 존재한다는 이유만으로 정상 운용을 계속해서는 안 된다. 해당 정보가 최신 상태인지, 충분히 신뢰할 수 있는지, 현재 기동에 필요한 최소 센싱 구성(Minimum Sensing Configuration)을 만족하는지를 확인해야 한다.

안전 검증(Safety Validation)은 정상 상태 시험만 수행하는 것이 아니라 의도적인 고장 주입(Fault Injection)을 포함해야 한다. 엔지니어는 센서 연결을 해제하거나, 메시지를 지연시키거나, 타임스탬프를 손상시키거나, 보정 오류를 발생시키거나, 카메라를 가리거나, 라이다 반환점을 감소시키거나, 프로세서에 과부하를 발생시키거나, 서로 일치하지 않는 센서 관측을 생성할 수 있다. 목적은 고장이 요구된 시간 내에 검출되고 로봇이 의도된 성능 저하 상태 또는 안전 상태로 예측 가능하게 전환되는지를 확인하는 것이다.

따라서 인지 안전은 하나의 고정확도 모델이 아니라 여러 계층(Layer)을 통해 달성된다. 센서 다양성(Sensor Diversity), 상태 모니터링, 불확실성 추정, 기하학적 안전장치(Geometric Safeguard), 시간적 유효성 검증, 교차 검증, 런타임 감시, 성능 저하 운용 및 독립적인 비상 보호 기능이 함께 위험을 감소시킨다. 각각의 계층은 다른 계층이 검출하지 못할 수 있는 고장 모드를 처리하여 전체 인지 아키텍처에 심층 방어(Defense in Depth)를 형성한다.

피지컬 AI(Physical AI) 시스템에서는 점점 강력해지는 학습 기반 인지가 이러한 계층형 안전 구조를 대체하는 것이 아니라 강화해야 한다. 파운데이션 모델(Foundation Model), 개방형 어휘 인지(Open-Vocabulary Perception), 시간적 추론(Temporal Reasoning), 다중모달 표현(Multimodal Representation)은 환경 이해 능력을 향상시킬 수 있지만, 그 출력은 명시적인 유효성 검사와 물리적 안전 메커니즘의 제약을 받아야 한다. 지능은 로봇이 이해할 수 있는 범위를 확장하고, 안전 아키텍처는 로봇에게 허용되는 행동의 범위를 제한한다.

궁극적으로 양산용 인지 시스템(Production Perception System)은 모든 것이 정상적으로 작동할 때 얼마나 높은 성능을 발휘하는지만이 아니라 문제가 발생했을 때 얼마나 예측 가능하게 동작하는지를 기준으로 평가해야 한다. 안전한 인지는 정보가 언제 유효한지를 판단하고, 신뢰도가 저하되는 상황을 검출하며, 중대한 위험에 대한 독립적인 보호 기능을 유지하고, 적절한 대체 동작(Fallback Response)을 선택할 수 있어야 한다. 고장 안전 인지(Fail-Safe Perception)는 피할 수 없는 불확실성과 고장을 통제되지 않은 물리적 위험이 아니라 통제 가능한 로봇 동작으로 전환한다.

##  

## 01.09. Perception Hardware Accelerators GPU DLA NPU

![](images/image9.png){width="7.268055555555556in" height="7.268055555555556in"}

Perception hardware accelerators provide the computational foundation that allows modern robots to process high-bandwidth sensor streams and execute complex neural networks within real-time constraints. Cameras, LiDAR, radar, and multimodal perception can generate large computational workloads that exceed the practical capability of general-purpose CPUs alone. GPUs, DLAs, and NPUs address this problem through specialized parallel architectures optimized for different classes of perception computation.

The CPU remains important even when dedicated accelerators are available. It typically manages sensor drivers, operating-system services, middleware, synchronization, coordinate transformations, control logic, scheduling, and portions of preprocessing or postprocessing. However, sequential CPU execution is inefficient for the large matrix operations and highly parallel numerical workloads used by modern deep neural networks, motivating the use of specialized accelerators.

A graphics processing unit, or GPU, contains many parallel execution units designed to perform large numbers of numerical operations simultaneously. Although originally developed for graphics, GPUs are highly effective for convolution, matrix multiplication, attention, tensor operations, point-cloud processing, and other workloads common in robot perception. Their programmability makes them the most flexible accelerator class for rapidly evolving AI algorithms.

GPU acceleration is particularly valuable when a robot executes heterogeneous perception workloads. A single GPU can run object detection, semantic segmentation, depth estimation, feature extraction, three-dimensional detection, sensor fusion, tracking-related neural components, and vision-language models. This flexibility allows researchers and engineers to deploy new network architectures without requiring a dedicated hardware design for each perception function.

The performance of a GPU cannot be understood from theoretical operations per second alone. Actual inference speed depends on tensor dimensions, memory access, kernel efficiency, numerical precision, batch size, sparsity, synchronization, and utilization. Robot workloads often use a batch size of one because observations must be processed immediately, which differs significantly from throughput-oriented data-center inference where large batches can keep hardware highly utilized.

Memory bandwidth is frequently as important as arithmetic capability. High-resolution images, feature pyramids, voxel tensors, bird\'s-eye-view representations, point-cloud features, and transformer activations can require substantial movement between memory and compute units. A model with relatively moderate arithmetic requirements can therefore become memory-bound. Accelerator selection should consequently consider memory capacity, bandwidth, and data locality together with nominal compute performance.

A deep learning accelerator, or DLA, is designed more specifically for neural-network inference than a general-purpose GPU. DLA hardware commonly accelerates operations such as convolution, activation, pooling, normalization, and tensor processing using highly optimized execution paths. Because it supports a narrower workload than a GPU, it can often provide better power efficiency for neural networks that map well onto its supported operator set.

DLA resources are useful for offloading stable perception networks from the GPU. For example, a validated detector or segmentation model may execute on a DLA while the GPU handles more dynamic workloads such as three-dimensional fusion, transformer inference, or experimental models. This division can improve total system utilization and reserve flexible GPU capacity for algorithms that cannot execute efficiently on the dedicated accelerator.

The main limitation of DLA-style acceleration is restricted operator and model support. Neural networks containing unsupported layers, dynamic operations, unusual tensor shapes, or newly introduced architectures may require partial execution on another processor. Such fallback can introduce memory transfers and synchronization overhead. A model that appears computationally suitable for DLA deployment must therefore be tested as a complete execution graph rather than evaluated only by layer count.

A neural processing unit, or NPU, similarly targets machine-learning computation but can vary significantly in architecture among vendors. NPUs generally provide specialized tensor engines, local memory structures, quantized arithmetic, and dataflow optimized for neural inference. They are increasingly integrated into embedded system-on-chip platforms, allowing perception models to execute with lower power consumption than would often be possible using a large discrete GPU.

NPU efficiency is especially attractive for robots constrained by battery capacity, thermal dissipation, size, or cost. Small AMRs, drones, quadrupeds, and mobile manipulators may not be able to support high-power discrete GPUs continuously. An NPU can execute selected detection, classification, segmentation, or feature-extraction networks while consuming a smaller portion of the robot\'s energy and cooling budget.

Quantization plays an important role in dedicated accelerators. Neural networks originally trained using floating-point arithmetic can often be converted to lower-precision formats such as FP16 or INT8 for deployment. Reduced precision decreases memory traffic and can increase inference throughput, but conversion can alter numerical behavior. Calibration and validation are therefore required to confirm that acceleration does not introduce unacceptable degradation in perception accuracy.

Mixed-precision execution allows different parts of a model to use different numerical formats. Operations sensitive to numerical error can remain at higher precision while robust layers execute using lower precision. Modern perception deployment therefore involves more than simply transferring a trained model onto an accelerator. Engineers must optimize the computational graph, supported operators, precision, memory layout, and execution scheduling for the selected hardware.

Heterogeneous computing combines CPUs, GPUs, DLAs, and NPUs so that each processor executes workloads suited to its architecture. Sensor management and system logic may remain on the CPU, stable convolutional networks may execute on a DLA or NPU, and flexible high-complexity perception can run on the GPU. This approach can provide higher efficiency than attempting to execute every component on one processor type.

However, heterogeneous acceleration introduces communication overhead. Moving tensors between CPU memory, accelerator memory, and separate devices consumes bandwidth and time. Synchronization barriers can further increase latency when one processing stage waits for another accelerator to complete. A theoretically faster hardware configuration can therefore produce worse end-to-end performance if the perception graph requires excessive transfers between processors.

Unified or shared memory architectures can reduce some of this overhead by allowing processors to access common physical memory without repeated explicit copies. Nevertheless, memory contention remains possible when cameras, neural networks, visualization, mapping, and other processes simultaneously demand bandwidth. System design must therefore consider the complete memory hierarchy rather than treating accelerator computation as an isolated resource.

Real-time scheduling is equally important because a robot commonly runs several perception models concurrently. Obstacle detection may have strict deadlines, localization may require frequent updates, while semantic scene understanding can tolerate slower execution. Accelerator scheduling should prioritize safety-critical workloads and prevent large low-priority networks from blocking time-sensitive inference. Multi-rate perception architectures can distribute computation according to task urgency.

Thermal behavior places another constraint on sustained accelerator performance. Peak benchmark performance may be available only for short periods before temperature or power limits reduce clock frequency. Robots can operate for hours in enclosed computing compartments or challenging outdoor temperatures, making sustained performance more important than short benchmark results. Thermal design, cooling, power delivery, and workload scheduling therefore directly influence perception reliability.

Power consumption also connects accelerator selection with robot mobility. Every watt consumed by computing reduces the energy available for propulsion, sensing, communication, and mission equipment. High-performance GPUs may be justified for complex Physical AI workloads, while smaller robots can benefit from efficient NPUs or DLAs. The appropriate architecture balances perception capability against battery endurance, thermal limits, physical size, and mission duration.

Hardware redundancy and graceful degradation can improve reliability. If a high-performance perception workload becomes unavailable because of accelerator failure or overload, a simpler network or geometric perception path may continue on another compute resource. Safety-critical perception should not assume unlimited accelerator availability. Watchdogs can monitor inference deadlines, memory use, device temperature, utilization, and process health to detect computational degradation.

Software ecosystems strongly influence accelerator practicality. Compiler support, runtime libraries, optimized kernels, model converters, debugging tools, profiling utilities, and framework compatibility determine how easily models can be deployed and maintained. A processor with impressive theoretical specifications may provide limited engineering value if important perception operators require custom implementation or if debugging deployment failures is difficult.

Benchmarking should therefore use representative robot workloads rather than isolated synthetic operations. Evaluation should measure end-to-end latency, sustained frame rate, memory usage, power consumption, temperature, model accuracy, and timing jitter on the intended hardware. Camera preprocessing, point-cloud conversion, tensor transfers, postprocessing, and sensor fusion should be included because accelerator inference time alone does not represent complete perception performance.

Future Physical AI systems are likely to increase the need for heterogeneous acceleration. Conventional convolutional perception is increasingly combined with transformers, vision-language models, three-dimensional world representations, temporal models, and learned planning components. These workloads have different compute and memory characteristics, making a single accelerator architecture unlikely to be optimal for every stage of the intelligence pipeline.

The correct accelerator strategy is therefore workload-driven rather than device-driven. GPUs provide broad programmability and strong parallel performance, DLAs offer efficient execution for supported deep-learning graphs, and NPUs provide specialized low-power neural inference. CPUs remain essential for orchestration and general computation. Combining these resources intelligently allows a robot to balance flexibility, latency, energy efficiency, and computational capacity.

Perception acceleration ultimately concerns the complete path from sensor measurement to actionable information. The fastest individual processor does not necessarily produce the fastest or safest robot. Effective hardware architecture minimizes unnecessary data movement, assigns workloads to appropriate compute engines, guarantees critical deadlines, manages thermal and power constraints, and preserves fallback capability. These principles transform raw accelerator performance into dependable real-time perception for autonomous robots.

인지 하드웨어 가속기(Perception Hardware Accelerator)는 현대 로봇이 높은 대역폭의 센서 스트림을 처리하고 복잡한 신경망(Neural Network)을 실시간 제약 조건 안에서 실행할 수 있도록 하는 계산 기반을 제공한다. 카메라, 라이다(LiDAR), 레이더(Radar), 다중모달 인지(Multimodal Perception)는 범용 CPU만으로 처리하기 어려운 대규모 계산 부하를 생성할 수 있다. GPU, DLA, NPU는 서로 다른 유형의 인지 계산에 최적화된 특수 병렬 아키텍처(Specialized Parallel Architecture)를 통해 이러한 문제를 해결한다.

전용 가속기를 사용할 수 있는 경우에도 CPU는 여전히 중요하다. 일반적으로 CPU는 센서 드라이버, 운영체제 서비스, 미들웨어(Middleware), 동기화, 좌표 변환, 제어 로직, 스케줄링 및 일부 전처리 또는 후처리를 관리한다. 그러나 순차적인 CPU 실행 방식은 현대 심층 신경망(Deep Neural Network)에서 사용되는 대규모 행렬 연산과 높은 병렬성을 가진 수치 계산에는 비효율적이므로 특수 가속기를 사용할 필요가 있다.

그래픽 처리 장치(Graphics Processing Unit, GPU)는 많은 수의 수치 연산을 동시에 수행하도록 설계된 다수의 병렬 실행 유닛(Parallel Execution Unit)을 포함한다. 원래 그래픽 처리를 위해 개발되었지만 GPU는 합성곱(Convolution), 행렬 곱셈(Matrix Multiplication), 어텐션(Attention), 텐서 연산(Tensor Operation), 포인트 클라우드 처리 및 로봇 인지에서 일반적으로 사용되는 다양한 계산에 매우 효과적이다. 높은 프로그래밍 유연성(Programmability)은 빠르게 변화하는 AI 알고리즘에 GPU가 가장 유연한 가속기 유형으로 활용되는 중요한 이유이다.

GPU 가속은 로봇이 이질적인 인지 작업(Heterogeneous Perception Workload)을 실행할 때 특히 유용하다. 하나의 GPU에서 객체 검출(Object Detection), 의미론적 분할(Semantic Segmentation), 깊이 추정(Depth Estimation), 특징 추출(Feature Extraction), 3차원 객체 검출, 센서 융합(Sensor Fusion), 추적 관련 신경망 구성요소 및 비전-언어 모델(Vision-Language Model)을 실행할 수 있다. 이러한 유연성을 통해 연구자와 엔지니어는 각각의 인지 기능을 위한 전용 하드웨어를 새로 설계하지 않고도 새로운 신경망 아키텍처를 배포할 수 있다.

GPU 성능은 이론적인 초당 연산량만으로 판단할 수 없다. 실제 추론 속도는 텐서 크기, 메모리 접근, 커널 효율(Kernel Efficiency), 수치 정밀도(Numerical Precision), 배치 크기(Batch Size), 희소성(Sparsity), 동기화 및 하드웨어 활용률(Utilization)에 영향을 받는다. 로봇의 작업 부하는 관측 정보를 즉시 처리해야 하기 때문에 배치 크기 1을 사용하는 경우가 많으며, 이는 큰 배치를 이용하여 하드웨어 활용률을 높이는 데이터센터 중심의 처리량 지향 추론과 상당히 다르다.

메모리 대역폭(Memory Bandwidth)은 연산 성능만큼 중요한 경우가 많다. 고해상도 영상, 특징 피라미드(Feature Pyramid), 복셀 텐서(Voxel Tensor), 조감도 표현(Bird\'s-Eye-View Representation), 포인트 클라우드 특징 및 트랜스포머 활성값(Transformer Activation)은 메모리와 연산 유닛 사이에서 상당한 데이터 이동을 요구할 수 있다. 따라서 연산량이 비교적 적은 모델도 메모리 병목(Memory-Bound)이 발생할 수 있다. 가속기를 선택할 때는 명목상의 계산 성능과 함께 메모리 용량, 대역폭 및 데이터 지역성(Data Locality)을 고려해야 한다.

딥러닝 가속기(Deep Learning Accelerator, DLA)는 범용 GPU보다 신경망 추론에 더욱 특화된 구조로 설계된다. DLA 하드웨어는 일반적으로 합성곱, 활성화(Activation), 풀링(Pooling), 정규화(Normalization), 텐서 처리와 같은 연산을 고도로 최적화된 실행 경로를 통해 가속한다. GPU보다 지원하는 작업 범위가 좁기 때문에 지원되는 연산자 집합(Operator Set)에 잘 대응하는 신경망에서는 더 높은 전력 효율(Power Efficiency)을 제공할 수 있다.

DLA 자원은 안정화된 인지 신경망을 GPU에서 분리하여 실행하는 데 유용하다. 예를 들어 검증된 객체 검출기 또는 분할 모델을 DLA에서 실행하는 동안 GPU는 3차원 융합, 트랜스포머 추론 또는 실험적인 모델과 같이 더 동적인 작업을 처리할 수 있다. 이러한 분배는 전체 시스템의 자원 활용률을 향상시키고 전용 가속기에서 효율적으로 실행하기 어려운 알고리즘을 위해 유연한 GPU 계산 자원을 확보할 수 있게 한다.

DLA 방식 가속의 주요 한계는 지원되는 연산자와 모델의 범위가 제한적이라는 점이다. 지원되지 않는 계층, 동적 연산(Dynamic Operation), 특이한 텐서 형태 또는 새롭게 등장한 아키텍처를 포함하는 신경망은 일부 연산을 다른 프로세서에서 실행해야 할 수 있다. 이러한 대체 실행(Fallback)은 메모리 전송과 동기화 오버헤드를 발생시킬 수 있다. 따라서 DLA 배포에 적합해 보이는 모델이라도 단순한 계층 수가 아니라 전체 실행 그래프(Execution Graph)를 기준으로 시험해야 한다.

신경망 처리 장치(Neural Processing Unit, NPU) 역시 머신러닝 계산을 대상으로 하지만 구체적인 아키텍처는 제조사마다 크게 다를 수 있다. NPU는 일반적으로 특수 텐서 엔진(Tensor Engine), 로컬 메모리 구조, 양자화 연산(Quantized Arithmetic), 신경망 추론에 최적화된 데이터 흐름(Dataflow)을 제공한다. NPU는 임베디드 시스템온칩(System-on-Chip, SoC)에 점점 더 많이 통합되고 있으며, 대형 외장 GPU를 사용하는 경우보다 낮은 전력으로 인지 모델을 실행할 수 있도록 한다.

NPU의 높은 효율성은 배터리 용량, 열 방출, 크기 또는 비용의 제약을 받는 로봇에서 특히 유용하다. 소형 자율이동로봇(AMR), 드론(Drone), 사족보행 로봇(Quadruped), 이동형 매니퓰레이터(Mobile Manipulator)는 고전력 외장 GPU를 지속적으로 운용하기 어려울 수 있다. NPU는 로봇의 에너지 및 냉각 예산 중 상대적으로 작은 부분을 사용하면서 특정 객체 검출, 분류, 분할 또는 특징 추출 신경망을 실행할 수 있다.

양자화(Quantization)는 전용 가속기에서 중요한 역할을 한다. 원래 부동소수점 연산(Floating-Point Arithmetic)을 사용하여 학습한 신경망을 배포할 때 FP16이나 INT8과 같은 낮은 정밀도의 형식으로 변환할 수 있다. 낮은 정밀도는 메모리 트래픽을 줄이고 추론 처리량을 증가시킬 수 있지만 변환 과정에서 수치적 동작이 달라질 수 있다. 따라서 가속 과정에서 인지 정확도가 허용할 수 없는 수준으로 저하되지 않는지 확인하기 위해 보정(Calibration)과 검증(Validation)이 필요하다.

혼합 정밀도 실행(Mixed-Precision Execution)을 사용하면 하나의 모델에서 서로 다른 부분에 서로 다른 수치 형식을 적용할 수 있다. 수치 오차에 민감한 연산은 높은 정밀도를 유지하고, 상대적으로 강건한 계층은 낮은 정밀도로 실행할 수 있다. 따라서 현대적인 인지 모델 배포는 학습된 모델을 단순히 가속기로 옮기는 것 이상의 작업을 요구한다. 선택한 하드웨어에 맞추어 계산 그래프, 지원 연산자, 정밀도, 메모리 배치(Memory Layout), 실행 스케줄링을 최적화해야 한다.

이기종 컴퓨팅(Heterogeneous Computing)은 CPU, GPU, DLA, NPU를 결합하여 각 프로세서가 자신의 아키텍처에 적합한 작업을 수행하도록 한다. 센서 관리와 시스템 로직은 CPU에서 유지하고, 안정화된 합성곱 신경망은 DLA 또는 NPU에서 실행하며, 유연하고 복잡도가 높은 인지 기능은 GPU에서 실행할 수 있다. 이러한 접근 방식은 모든 구성요소를 하나의 프로세서 유형에서 실행하는 것보다 높은 효율성을 제공할 수 있다.

그러나 이기종 가속은 통신 오버헤드(Communication Overhead)를 발생시킨다. CPU 메모리, 가속기 메모리 및 서로 분리된 장치 사이에서 텐서를 이동시키는 과정은 대역폭과 시간을 소비한다. 하나의 처리 단계가 다른 가속기의 완료를 기다리는 경우 동기화 장벽(Synchronization Barrier)이 지연시간을 더욱 증가시킬 수 있다. 따라서 이론적으로 더 빠른 하드웨어 구성을 사용하더라도 인지 그래프에서 프로세서 사이의 데이터 전송이 지나치게 많으면 종단간 성능(End-to-End Performance)은 오히려 저하될 수 있다.

통합 메모리(Unified Memory) 또는 공유 메모리 아키텍처(Shared Memory Architecture)는 여러 프로세서가 반복적인 명시적 복사 없이 공통 물리 메모리에 접근할 수 있도록 하여 이러한 오버헤드의 일부를 줄일 수 있다. 그러나 카메라, 신경망, 시각화, 매핑 및 기타 프로세스가 동시에 대역폭을 요구하면 메모리 자원 경쟁(Memory Contention)이 발생할 수 있다. 따라서 시스템 설계에서는 가속기 계산을 독립적인 자원으로 취급하기보다 전체 메모리 계층 구조(Memory Hierarchy)를 함께 고려해야 한다.

로봇은 일반적으로 여러 인지 모델을 동시에 실행하므로 실시간 스케줄링(Real-Time Scheduling) 역시 중요하다. 장애물 검출은 엄격한 실행 마감시간(Deadline)을 가질 수 있고, 위치추정은 높은 갱신 빈도가 필요하며, 의미론적 장면 이해는 상대적으로 느린 실행을 허용할 수 있다. 가속기 스케줄링은 안전 중요 작업(Safety-Critical Workload)을 우선하고 대규모 저우선순위 신경망이 시간에 민감한 추론을 방해하지 않도록 해야 한다. 다중 주기 인지 아키텍처(Multi-Rate Perception Architecture)는 작업의 긴급도에 따라 계산 자원을 분배할 수 있다.

열적 특성(Thermal Behavior)은 지속적인 가속기 성능에 또 다른 제약을 제공한다. 최대 벤치마크 성능은 온도 또는 전력 제한으로 인해 클럭 주파수가 낮아지기 전까지 짧은 시간 동안만 유지될 수 있다. 로봇은 밀폐된 컴퓨팅 공간이나 열악한 실외 온도에서 수 시간 동안 동작할 수 있기 때문에 순간적인 벤치마크 성능보다 지속 성능(Sustained Performance)이 더욱 중요하다. 따라서 열 설계, 냉각, 전력 공급 및 작업 스케줄링은 인지 신뢰성에 직접적인 영향을 미친다.

전력 소비(Power Consumption)는 가속기 선택을 로봇의 이동 성능과 연결한다. 컴퓨팅에 사용되는 모든 전력은 추진, 센싱, 통신 및 임무 장비에 사용할 수 있는 에너지를 감소시킨다. 복잡한 피지컬 AI(Physical AI) 작업에서는 고성능 GPU를 사용하는 것이 타당할 수 있지만, 소형 로봇은 고효율 NPU 또는 DLA를 통해 더 큰 이점을 얻을 수 있다. 적절한 아키텍처는 인지 능력을 배터리 지속시간, 열적 한계, 물리적 크기 및 임무 지속시간과 균형 있게 조정해야 한다.

하드웨어 중복성(Hardware Redundancy)과 점진적 성능 저하(Graceful Degradation)는 신뢰성을 향상시킬 수 있다. 가속기 고장이나 과부하로 고성능 인지 작업을 사용할 수 없게 되더라도 더 단순한 신경망 또는 기하학적 인지 경로(Geometric Perception Path)가 다른 계산 자원에서 계속 동작할 수 있다. 안전 중요 인지는 가속기 자원이 항상 무제한으로 사용 가능하다고 가정해서는 안 된다. 감시 장치(Watchdog)는 추론 마감시간, 메모리 사용량, 장치 온도, 활용률 및 프로세스 상태를 모니터링하여 계산 성능 저하를 검출할 수 있다.

소프트웨어 생태계(Software Ecosystem)는 가속기의 실용성에 큰 영향을 미친다. 컴파일러 지원, 런타임 라이브러리(Runtime Library), 최적화된 커널, 모델 변환기(Model Converter), 디버깅 도구, 프로파일링 유틸리티(Profiling Utility), 프레임워크 호환성은 모델을 얼마나 쉽게 배포하고 유지보수할 수 있는지를 결정한다. 이론적인 사양이 뛰어난 프로세서라도 중요한 인지 연산자를 직접 구현해야 하거나 배포 오류를 디버깅하기 어렵다면 실제 엔지니어링 가치는 제한될 수 있다.

따라서 벤치마킹(Benchmarking)은 독립적인 합성 연산이 아니라 실제 로봇을 대표하는 작업 부하를 사용해야 한다. 목표 하드웨어에서 종단간 지연시간, 지속 프레임률(Sustained Frame Rate), 메모리 사용량, 전력 소비, 온도, 모델 정확도 및 시간 지터(Timing Jitter)를 측정해야 한다. 가속기의 추론 시간만으로는 전체 인지 성능을 나타낼 수 없기 때문에 카메라 전처리, 포인트 클라우드 변환, 텐서 전송, 후처리 및 센서 융합도 평가에 포함해야 한다.

미래의 피지컬 AI 시스템은 이기종 가속(Heterogeneous Acceleration)의 필요성을 더욱 증가시킬 가능성이 높다. 기존의 합성곱 기반 인지와 함께 트랜스포머(Transformer), 비전-언어 모델, 3차원 월드 표현(3D World Representation), 시간 모델(Temporal Model), 학습 기반 경로 계획 구성요소가 점점 더 결합되고 있다. 이러한 작업들은 서로 다른 계산 및 메모리 특성을 가지므로 하나의 가속기 아키텍처가 전체 지능 파이프라인의 모든 단계에 최적일 가능성은 낮다.

따라서 올바른 가속기 전략은 장치 중심(Device-Driven)이 아니라 작업 부하 중심(Workload-Driven)으로 결정해야 한다. GPU는 폭넓은 프로그래밍 유연성과 높은 병렬 처리 성능을 제공하고, DLA는 지원되는 딥러닝 그래프를 효율적으로 실행하며, NPU는 특수화된 저전력 신경망 추론을 제공한다. CPU는 오케스트레이션(Orchestration)과 범용 계산에서 여전히 필수적이다. 이러한 자원을 지능적으로 결합하면 로봇은 유연성, 지연시간, 에너지 효율 및 계산 능력 사이에서 균형을 확보할 수 있다.

인지 가속(Perception Acceleration)은 궁극적으로 센서 측정에서 행동 가능한 정보(Actionable Information)에 이르는 전체 경로의 문제이다. 가장 빠른 개별 프로세서가 반드시 가장 빠르거나 가장 안전한 로봇을 만드는 것은 아니다. 효과적인 하드웨어 아키텍처는 불필요한 데이터 이동을 최소화하고, 작업을 적절한 계산 엔진에 할당하며, 중요한 실행 마감시간을 보장하고, 열 및 전력 제약을 관리하며, 대체 실행 능력(Fallback Capability)을 유지해야 한다. 이러한 원칙을 통해 단순한 가속기 성능을 자율 로봇을 위한 신뢰할 수 있는 실시간 인지(Real-Time Perception) 능력으로 전환할 수 있다.

##  

## 01.10. Perception Architecture Comparison by Robot Platform

![](images/image10.png){width="7.268055555555556in" height="7.268055555555556in"}

Robot perception architecture should be selected according to the physical platform rather than treated as a universal software stack. An indoor AMR, outdoor autonomous robot, quadruped, manipulator, humanoid, and aerial robot operate under different motion dynamics, sensing geometries, power limits, safety requirements, and interaction patterns. These differences determine sensor placement, perception rate, representation, computing architecture, and the degree of semantic understanding required.

Indoor autonomous mobile robots usually operate on relatively flat floors within warehouses, factories, hospitals, or commercial facilities. Their perception architecture emphasizes reliable localization, obstacle detection, free-space estimation, and human awareness. Two-dimensional or three-dimensional LiDAR, depth cameras, wheel odometry, and IMUs are common inputs, while occupancy grids and local costmaps provide efficient representations for navigation and collision avoidance.

The indoor AMR architecture can remain relatively modular because the environment and mission are usually structured. Localization, mapping, obstacle detection, tracking, and navigation can operate as separate components connected through well-defined interfaces. High-level semantic perception may identify people, pallets, doors, racks, or docking stations, but geometric obstacle detection should remain available independently because collision avoidance cannot depend entirely on correct object classification.

Outdoor mobile robots face substantially greater environmental variability. Uneven terrain, slopes, vegetation, rain, fog, shadows, direct sunlight, dust, moving vehicles, and long sensing distances increase perception complexity. Their architecture commonly combines cameras, three-dimensional LiDAR, radar, GNSS RTK, and IMUs so that geometric, visual, motion, and global positioning information remain available under changing operating conditions.

Outdoor perception requires richer spatial representations than a simple planar occupancy grid. Three-dimensional point clouds, elevation maps, voxel maps, traversability layers, semantic terrain maps, and bird\'s-eye-view representations can describe terrain geometry and obstacle structure. The perception system must distinguish between physically occupied space and terrain that is technically free but unsafe to traverse because of slope, roughness, softness, drop-offs, or insufficient clearance.

Outdoor platforms also require strong temporal reasoning because objects and the robot itself may move at significant speed. Detection and tracking should estimate position, velocity, trajectory, and uncertainty while ego-motion compensation aligns measurements acquired at different times. Radar becomes particularly useful for motion information and adverse conditions, while camera and LiDAR information provide complementary semantic and geometric detail.

Quadruped robots introduce a different perception problem because locomotion depends directly on local terrain geometry. Their architecture requires rapid estimation of foothold regions, surface orientation, step height, gaps, stairs, edges, and terrain stability. Cameras and LiDAR may provide global environmental understanding, but short-range depth sensing and proprioceptive information are especially important for controlling interactions between the feet and the ground.

Perception for quadrupeds must therefore operate across multiple spatial and temporal scales. Long-range perception supports route selection and obstacle avoidance, while local terrain perception supports foot placement and body stabilization. IMU measurements, joint encoders, contact sensing, and external perception must be fused so that the robot can distinguish environmental geometry from disturbances caused by its own body motion.

Manipulators shift the emphasis from navigation-scale perception toward precise object and interaction understanding. Their perception architecture commonly requires RGB or RGB-D cameras, wrist-mounted cameras, force or torque sensing, tactile sensors, and accurate robot kinematics. Object identity alone is insufficient; the system often needs six-degree-of-freedom pose, surface geometry, grasp points, part relationships, and affordances describing how an object can be manipulated.

Manipulation also requires perception at much finer spatial resolution than many mobile navigation tasks. Millimeter-scale pose errors can cause failed grasps or collisions even when object classification is correct. Eye-to-hand and eye-in-hand calibration, depth quality, occlusion handling, object tracking, and uncertainty estimation therefore become central architectural concerns. Tactile and force perception can further close the loop after visual information becomes unreliable during physical contact.

Mobile manipulators combine the requirements of navigation and manipulation. The robot must maintain large-scale localization and obstacle awareness while simultaneously supporting precise object interaction near the arm. A hierarchical architecture is therefore useful: navigation perception maintains global and local environmental models, while manipulation perception activates higher-resolution sensing and object-centric representations when the robot approaches its work target.

Humanoid robots require an even broader perception architecture because they combine locomotion, manipulation, human interaction, and semantic reasoning within one platform. Cameras, depth sensors, LiDAR, IMUs, joint encoders, force-torque sensors, tactile sensing, microphones, and potentially other modalities can contribute to a unified representation. Perception must support balance, terrain understanding, object manipulation, human awareness, and task interpretation concurrently.

Humanoid perception benefits strongly from hierarchical and multimodal representations. Low-level geometric and proprioceptive perception supports balance and collision avoidance, object-level representations support manipulation, and scene graphs or semantic maps support reasoning about places and relationships. Vision-language embeddings can connect observations with instructions, while temporal models preserve object states and human actions across extended interactions.

Aerial robots impose severe constraints on mass, energy consumption, computation, and sensing range. Small drones commonly rely on cameras, IMUs, altimeters, GNSS, and lightweight depth sensors, while larger aerial systems may carry LiDAR or radar. Visual-inertial odometry is particularly important where GNSS is unavailable, and perception latency must remain low because high vehicle speed can rapidly convert estimation errors into large spatial deviations.

Aerial perception architecture must also account for six-degree-of-freedom vehicle motion. Unlike ground robots, drones cannot assume that the sensing platform remains approximately level or constrained to a surface. Feature tracking, depth estimation, obstacle detection, and mapping must remain stable during rapid translation and rotation. Compute efficiency is especially important because accelerator power consumption directly competes with propulsion for limited onboard energy.

Industrial inspection robots introduce another architectural variation. Navigation perception may only need sufficient accuracy to position the platform near an inspection target, while inspection perception can demand extremely high image resolution, precise viewpoint control, anomaly detection, three-dimensional reconstruction, or comparison against CAD models. Separating mobility perception from inspection perception allows each subsystem to operate at the accuracy, rate, and computational scale appropriate to its task.

Sensor placement differs substantially among platforms. An AMR benefits from near-360-degree horizontal coverage, an outdoor vehicle requires long-range forward and surrounding perception, a quadruped needs both forward environmental sensing and downward terrain observation, and a manipulator benefits from fixed workspace cameras combined with wrist-mounted sensing. Humanoids often require head-centered sensing together with distributed body and contact sensors.

The required perception update rate also follows platform dynamics. Slowly moving semantic mapping can operate at relatively low frequency, while obstacle avoidance, legged locomotion, aerial stabilization, and contact control require much faster information. A practical architecture therefore separates perception according to temporal criticality rather than forcing every algorithm to operate at the same rate. Fast geometric loops can coexist with slower semantic reasoning.

Computing architecture should similarly match the platform. Indoor AMRs may operate effectively with embedded CPU-GPU systems, while high-performance outdoor robots can justify multiple accelerators for camera, LiDAR, fusion, and AI workloads. Small drones and compact robots favor power-efficient SoCs and NPUs. Humanoids and advanced Physical AI platforms may require heterogeneous CPU, GPU, and dedicated accelerator combinations to execute multimodal models while preserving real-time control.

Safety architecture also varies by platform but should remain independent from unrestricted semantic intelligence. An AMR can use certified protective scanners, an outdoor robot can maintain geometric obstacle and stopping corridors, and a manipulator can enforce workspace and force limits. A quadruped may require rapid stabilization or controlled sitting behavior, while an aerial robot may require emergency landing logic when localization or obstacle perception becomes unreliable.

Platform-specific perception should nevertheless expose common abstractions to higher-level intelligence. Objects, occupancy, free space, terrain, robot pose, human state, semantic regions, uncertainty, and temporal history can be represented through standardized interfaces even when the underlying sensors differ. This separation allows planning and reasoning systems to consume consistent world information without depending directly on every hardware-specific sensing mechanism.

The major architectural difference is therefore not simply the number of sensors but the physical questions that perception must answer. An AMR asks where it can safely drive, an outdoor robot asks where it can traverse under uncertain terrain conditions, a quadruped additionally asks where each foot can land, and a manipulator asks how an object can be physically contacted and moved. A humanoid must answer many of these questions simultaneously.

No single perception architecture is optimal across all robot platforms. The appropriate design emerges from locomotion type, interaction mode, environmental variability, required precision, sensor geometry, compute resources, energy limits, latency constraints, and safety consequences. Architecture should therefore begin with the physical capabilities and mission of the robot and then determine the sensing, representation, computation, and inference layers needed to support them.

For Physical AI, the long-term direction is toward a shared hierarchical perception framework with platform-specific lower layers and increasingly common higher-level representations. Sensors and real-time geometric processing remain tightly coupled to each robot\'s embodiment, while objects, semantics, affordances, scene relationships, temporal state, and learned embeddings can become more transferable across platforms. This architecture preserves embodiment-specific safety while enabling broader reusable intelligence.

Perception architecture comparison by robot platform ultimately demonstrates that embodiment determines what information matters, how quickly it must be produced, and how accurately it must describe the physical world. Effective systems combine platform-specific sensing and real-time safety pathways with reusable semantic and learned representations. The result is not one universal perception stack, but a family of architectures organized around the physical demands of autonomous action.

로봇 인지 아키텍처(Robot Perception Architecture)는 모든 로봇에 공통으로 적용되는 하나의 범용 소프트웨어 스택으로 취급하기보다 물리적 플랫폼(Physical Platform)에 따라 선택해야 한다. 실내 자율이동로봇(Indoor AMR), 실외 자율 로봇(Outdoor Autonomous Robot), 사족보행 로봇(Quadruped), 매니퓰레이터(Manipulator), 휴머노이드(Humanoid), 비행 로봇(Aerial Robot)은 서로 다른 운동 동역학, 센싱 기하 구조, 전력 제한, 안전 요구사항 및 상호작용 방식을 가진다. 이러한 차이는 센서 배치, 인지 주기, 표현 방식, 컴퓨팅 아키텍처 및 필요한 의미론적 이해 수준을 결정한다.

실내 자율이동로봇(Indoor Autonomous Mobile Robot)은 일반적으로 창고, 공장, 병원 또는 상업 시설의 비교적 평탄한 바닥에서 운용된다. 이러한 로봇의 인지 아키텍처는 신뢰할 수 있는 위치추정(Localization), 장애물 검출(Obstacle Detection), 자유 공간 추정(Free-Space Estimation), 사람 인식(Human Awareness)을 중요하게 다룬다. 2차원 또는 3차원 라이다(LiDAR), 깊이 카메라(Depth Camera), 휠 오도메트리(Wheel Odometry), 관성측정장치(IMU)가 일반적인 입력이며, 점유 격자(Occupancy Grid)와 지역 비용 지도(Local Costmap)는 내비게이션 및 충돌 회피를 위한 효율적인 표현을 제공한다.

실내 AMR 아키텍처는 환경과 임무가 일반적으로 구조화되어 있기 때문에 비교적 모듈형(Modular)으로 구성할 수 있다. 위치추정, 매핑(Mapping), 장애물 검출, 추적(Tracking), 내비게이션은 명확하게 정의된 인터페이스를 통해 연결된 독립적인 구성요소로 동작할 수 있다. 상위 수준의 의미론적 인지는 사람, 팔레트, 문, 랙(Rack), 도킹 스테이션(Docking Station)을 식별할 수 있지만, 충돌 회피가 객체 분류의 정확성에 전적으로 의존해서는 안 되므로 기하학적 장애물 검출(Geometric Obstacle Detection)은 독립적으로 유지되어야 한다.

실외 이동 로봇(Outdoor Mobile Robot)은 훨씬 더 큰 환경적 다양성에 직면한다. 불규칙한 지형, 경사, 식생, 비, 안개, 그림자, 직사광선, 먼지, 이동 차량 및 긴 센싱 거리는 인지 복잡도를 증가시킨다. 이러한 로봇의 아키텍처는 일반적으로 카메라, 3차원 라이다, 레이더(Radar), GNSS RTK 및 IMU를 결합하여 변화하는 운용 조건에서도 기하학적 정보, 시각 정보, 운동 정보 및 전역 위치 정보를 유지할 수 있도록 한다.

실외 인지는 단순한 평면 점유 격자보다 풍부한 공간 표현(Spatial Representation)을 필요로 한다. 3차원 포인트 클라우드(Point Cloud), 고도 지도(Elevation Map), 복셀 지도(Voxel Map), 주행 가능성 계층(Traversability Layer), 의미론적 지형 지도(Semantic Terrain Map), 조감도 표현(Bird\'s-Eye-View Representation)은 지형의 기하 구조와 장애물 구조를 표현할 수 있다. 인지 시스템은 물리적으로 점유된 공간뿐 아니라 경사, 거칠기, 연약한 지면, 단차, 낭떠러지 또는 부족한 지상고 때문에 기술적으로는 비어 있지만 안전하게 주행할 수 없는 지형도 구분해야 한다.

실외 플랫폼은 객체와 로봇 자체가 상당한 속도로 이동할 수 있기 때문에 강력한 시간적 추론(Temporal Reasoning)도 필요하다. 객체 검출과 추적은 위치, 속도, 궤적 및 불확실성을 추정해야 하며, 자기 운동 보상(Ego-Motion Compensation)을 이용하여 서로 다른 시점에 획득된 측정값을 정렬해야 한다. 레이더는 운동 정보와 악조건에서 특히 유용하며, 카메라와 라이다 정보는 상호 보완적인 의미론적 정보와 기하학적 세부 정보를 제공한다.

사족보행 로봇(Quadruped Robot)은 보행이 국부적인 지형 기하 구조에 직접적으로 의존하기 때문에 다른 형태의 인지 문제를 가진다. 이러한 로봇의 아키텍처는 발 디딤 영역(Foothold Region), 표면 방향(Surface Orientation), 단차 높이, 틈, 계단, 모서리 및 지형 안정성을 빠르게 추정해야 한다. 카메라와 라이다는 전역적인 환경 이해를 제공할 수 있지만, 발과 지면 사이의 상호작용을 제어하기 위해서는 근거리 깊이 센싱(Short-Range Depth Sensing)과 고유수용성 정보(Proprioceptive Information)가 특히 중요하다.

따라서 사족보행 로봇의 인지는 여러 공간 및 시간 규모에서 동작해야 한다. 장거리 인지는 경로 선택과 장애물 회피를 지원하고, 국부 지형 인지(Local Terrain Perception)는 발 배치(Foot Placement)와 차체 안정화를 지원한다. IMU 측정값, 관절 인코더(Joint Encoder), 접촉 센싱(Contact Sensing), 외부 인지(External Perception)를 융합하여 로봇이 환경의 실제 기하 구조와 자신의 몸체 운동으로 인해 발생한 교란을 구분할 수 있어야 한다.

매니퓰레이터(Manipulator)는 내비게이션 규모의 인지보다 정밀한 객체 및 상호작용 이해(Object and Interaction Understanding)에 더 큰 비중을 둔다. 인지 아키텍처에는 일반적으로 RGB 또는 RGB-D 카메라, 손목 장착 카메라(Wrist-Mounted Camera), 힘 또는 토크 센싱(Force or Torque Sensing), 촉각 센서(Tactile Sensor), 정확한 로봇 운동학(Robot Kinematics)이 필요하다. 객체의 정체성만으로는 충분하지 않으며, 시스템은 6자유도 자세(Six-Degree-of-Freedom Pose), 표면 기하 구조, 파지점(Grasp Point), 부품 관계 및 객체를 어떻게 조작할 수 있는지를 나타내는 어포던스(Affordance)를 필요로 하는 경우가 많다.

조작(Manipulation)은 많은 이동 내비게이션 작업보다 훨씬 정밀한 공간 해상도의 인지를 요구한다. 객체 분류가 정확하더라도 밀리미터 수준의 자세 오차가 파지 실패나 충돌을 일으킬 수 있다. 따라서 외부 카메라 기반 보정(Eye-to-Hand Calibration), 손목 카메라 기반 보정(Eye-in-Hand Calibration), 깊이 품질, 가림 처리(Occlusion Handling), 객체 추적 및 불확실성 추정이 핵심적인 아키텍처 요소가 된다. 물리적 접촉이 시작되어 시각 정보의 신뢰성이 감소하면 촉각 및 힘 인지가 피드백 루프를 보완할 수 있다.

이동형 매니퓰레이터(Mobile Manipulator)는 내비게이션과 조작의 요구사항을 결합한다. 로봇은 대규모 위치추정과 장애물 인식을 유지하면서 동시에 로봇 팔 주변에서 정밀한 객체 상호작용을 지원해야 한다. 따라서 계층형 아키텍처(Hierarchical Architecture)가 유용하다. 내비게이션 인지는 전역 및 지역 환경 모델을 유지하고, 로봇이 작업 대상에 접근하면 조작 인지가 더 높은 해상도의 센싱과 객체 중심 표현(Object-Centric Representation)을 활성화할 수 있다.

휴머노이드 로봇(Humanoid Robot)은 하나의 플랫폼에서 보행, 조작, 인간과의 상호작용 및 의미론적 추론을 결합하기 때문에 더욱 광범위한 인지 아키텍처를 요구한다. 카메라, 깊이 센서, 라이다, IMU, 관절 인코더, 힘-토크 센서(Force-Torque Sensor), 촉각 센싱, 마이크 및 잠재적으로 다른 모달리티가 통합된 표현에 기여할 수 있다. 인지는 균형 유지, 지형 이해, 객체 조작, 사람 인식 및 작업 해석을 동시에 지원해야 한다.

휴머노이드 인지는 계층적·다중모달 표현(Hierarchical and Multimodal Representation)에서 큰 이점을 얻는다. 저수준 기하학적 인지와 고유수용성 인지는 균형과 충돌 회피를 지원하고, 객체 수준 표현은 조작을 지원하며, 장면 그래프(Scene Graph) 또는 의미 지도(Semantic Map)는 장소와 관계에 대한 추론을 지원한다. 비전-언어 임베딩(Vision-Language Embedding)은 관측 결과와 명령을 연결할 수 있으며, 시간 모델(Temporal Model)은 장시간의 상호작용 동안 객체 상태와 사람의 행동을 유지할 수 있다.

비행 로봇(Aerial Robot)은 질량, 에너지 소비, 계산 능력 및 센싱 거리에 대한 엄격한 제약을 가진다. 소형 드론(Drone)은 일반적으로 카메라, IMU, 고도계(Altimeter), GNSS 및 경량 깊이 센서를 사용하며, 더 큰 비행 플랫폼은 라이다나 레이더를 탑재할 수 있다. GNSS를 사용할 수 없는 환경에서는 시각-관성 오도메트리(Visual-Inertial Odometry)가 특히 중요하며, 높은 이동 속도로 인해 추정 오차가 빠르게 큰 공간 오차로 변할 수 있으므로 인지 지연시간을 낮게 유지해야 한다.

비행 로봇의 인지 아키텍처는 6자유도 차량 운동(Six-Degree-of-Freedom Vehicle Motion)도 고려해야 한다. 지상 로봇과 달리 드론은 센싱 플랫폼이 거의 수평 상태를 유지하거나 특정 표면에 제한되어 움직인다고 가정할 수 없다. 특징 추적(Feature Tracking), 깊이 추정, 장애물 검출 및 매핑은 빠른 병진 및 회전 운동에서도 안정적으로 유지되어야 한다. 또한 가속기의 전력 소비가 제한된 탑재 에너지를 추진 시스템과 직접적으로 경쟁하므로 계산 효율성(Compute Efficiency)이 특히 중요하다.

산업용 검사 로봇(Industrial Inspection Robot)은 또 다른 형태의 아키텍처를 요구한다. 이동 인지(Mobility Perception)는 플랫폼을 검사 대상 근처에 배치할 수 있을 정도의 정확도만 요구할 수 있지만, 검사 인지(Inspection Perception)는 매우 높은 영상 해상도, 정밀한 관측 시점 제어, 이상 검출(Anomaly Detection), 3차원 재구성 또는 CAD 모델과의 비교를 요구할 수 있다. 이동 인지와 검사 인지를 분리하면 각 하위 시스템을 작업에 적합한 정확도, 처리 주기 및 계산 규모로 운용할 수 있다.

센서 배치(Sensor Placement)는 플랫폼에 따라 크게 달라진다. AMR은 수평 방향의 거의 360도 센싱 범위에서 이점을 얻고, 실외 차량은 장거리 전방 및 주변 인지가 필요하며, 사족보행 로봇은 전방 환경 센싱과 하향 지형 관측을 모두 필요로 한다. 매니퓰레이터는 고정형 작업공간 카메라와 손목 장착 센싱을 결합하는 것이 유리하다. 휴머노이드는 일반적으로 머리 중심의 센싱과 함께 몸 전체에 분산된 센서 및 접촉 센서를 필요로 한다.

필요한 인지 갱신 주기(Perception Update Rate) 역시 플랫폼의 동역학에 따라 결정된다. 천천히 변화하는 의미론적 매핑(Semantic Mapping)은 비교적 낮은 주기로 동작할 수 있지만, 장애물 회피, 보행 로봇 이동, 비행 안정화 및 접촉 제어에는 훨씬 빠른 정보가 필요하다. 따라서 실제적인 아키텍처는 모든 알고리즘을 동일한 주기로 강제하기보다 시간적 중요도(Temporal Criticality)에 따라 인지를 분리한다. 빠른 기하학적 루프(Geometric Loop)와 느린 의미론적 추론을 동시에 운용할 수 있다.

컴퓨팅 아키텍처(Computing Architecture) 역시 플랫폼에 맞추어 구성해야 한다. 실내 AMR은 임베디드 CPU-GPU 시스템만으로도 효과적으로 동작할 수 있지만, 고성능 실외 로봇은 카메라, 라이다, 융합 및 AI 작업을 처리하기 위해 여러 가속기를 사용하는 것이 타당할 수 있다. 소형 드론과 소형 로봇은 전력 효율적인 시스템온칩(SoC)과 NPU가 유리하다. 휴머노이드 및 고급 피지컬 AI 플랫폼은 실시간 제어를 유지하면서 다중모달 모델을 실행하기 위해 CPU, GPU 및 전용 가속기를 결합한 이기종 컴퓨팅(Heterogeneous Computing)이 필요할 수 있다.

안전 아키텍처(Safety Architecture) 역시 플랫폼에 따라 달라지지만 제한 없는 의미론적 지능과 독립적으로 유지되어야 한다. AMR은 인증된 보호용 스캐너(Protective Scanner)를 사용할 수 있고, 실외 로봇은 기하학적 장애물 영역과 정지 경로를 유지할 수 있으며, 매니퓰레이터는 작업공간 및 힘 제한을 적용할 수 있다. 사족보행 로봇은 빠른 안정화 또는 제어된 착석 동작이 필요할 수 있으며, 비행 로봇은 위치추정이나 장애물 인지의 신뢰성이 저하될 경우 비상 착륙(Emergency Landing) 로직이 필요할 수 있다.

플랫폼별 인지는 서로 다르게 구성되더라도 상위 수준 지능(Higher-Level Intelligence)에 공통된 추상화(Common Abstraction)를 제공해야 한다. 객체, 점유 공간, 자유 공간, 지형, 로봇 자세, 사람 상태, 의미론적 영역, 불확실성 및 시간 이력(Temporal History)은 기본 센서가 서로 다르더라도 표준화된 인터페이스를 통해 표현할 수 있다. 이러한 분리를 통해 경로 계획과 추론 시스템은 모든 하드웨어별 센싱 메커니즘에 직접 의존하지 않고 일관된 세계 정보를 사용할 수 있다.

따라서 주요 아키텍처 차이는 단순히 센서 개수의 차이가 아니라 인지가 해결해야 하는 물리적 질문의 차이이다. AMR은 어디로 안전하게 주행할 수 있는지를 판단해야 하고, 실외 로봇은 불확실한 지형 조건에서 어디를 통과할 수 있는지를 판단해야 한다. 사족보행 로봇은 추가적으로 각각의 발을 어디에 디딜 수 있는지를 판단해야 하며, 매니퓰레이터는 객체와 어떻게 물리적으로 접촉하고 움직일 수 있는지를 판단해야 한다. 휴머노이드는 이러한 질문 중 다수를 동시에 해결해야 한다.

모든 로봇 플랫폼에 최적인 하나의 인지 아키텍처는 존재하지 않는다. 적절한 설계는 이동 방식(Locomotion Type), 상호작용 방식, 환경의 다양성, 요구 정밀도, 센서 기하 구조, 계산 자원, 에너지 제한, 지연시간 제약 및 안전상 결과에 따라 결정된다. 따라서 아키텍처 설계는 로봇의 물리적 능력과 임무에서 시작하여 이를 지원하기 위해 필요한 센싱, 표현, 계산 및 추론 계층을 결정하는 방식으로 진행해야 한다.

피지컬 AI(Physical AI)의 장기적인 방향은 플랫폼별 하위 계층(Platform-Specific Lower Layer)과 점차 공통화되는 상위 수준 표현을 결합한 공유 계층형 인지 프레임워크(Shared Hierarchical Perception Framework)로 발전하는 것이다. 센서와 실시간 기하학적 처리는 각 로봇의 신체적 구현(Embodiment)과 긴밀하게 결합된 상태를 유지하지만, 객체, 의미론, 어포던스, 장면 관계, 시간적 상태 및 학습된 임베딩은 플랫폼 사이에서 더욱 쉽게 공유될 수 있다. 이러한 아키텍처는 신체 구현에 특화된 안전성을 유지하면서 보다 광범위하게 재사용 가능한 지능을 가능하게 한다.

로봇 플랫폼별 인지 아키텍처 비교(Perception Architecture Comparison by Robot Platform)는 궁극적으로 신체적 구현이 어떤 정보가 중요한지, 그 정보가 얼마나 빠르게 생성되어야 하는지, 그리고 물리적 세계를 얼마나 정확하게 표현해야 하는지를 결정한다는 사실을 보여준다. 효과적인 시스템은 플랫폼에 특화된 센싱 및 실시간 안전 경로와 재사용 가능한 의미론적·학습 기반 표현을 결합한다. 그 결과는 하나의 범용 인지 스택이 아니라 자율 행동(Autonomous Action)의 물리적 요구사항을 중심으로 구성된 인지 아키텍처의 계열(Family of Architectures)이다.
