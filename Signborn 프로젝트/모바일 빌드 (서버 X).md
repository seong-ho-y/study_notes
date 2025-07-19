핵심은 **클라이언트(앱) 자체만으로 모든 게임 기능을 구현하고 데이터를 관리**하는 것입니다.

---

### 1. Unity 에디터 및 기본 개발 환경

Unity는 모바일 게임 개발의 중심 툴입니다.

- **Unity Editor:** 게임 오브젝트 배치, 스크립트 작성, UI 구성 등 모든 개발 작업이 이루어지는 통합 개발 환경입니다.
    
    - **Scene:** 게임의 각 화면(레벨, 메뉴 등)을 시각적으로 구성하는 작업 공간입니다.
        
    - **GameObject & Component:** Unity의 모든 구성 요소. GameObject에 다양한 Component (스크립트, 렌더러, 물리 엔진 등)를 붙여 기능을 만듭니다.
        
    - **Prefab:** 재사용 가능한 GameObject 템플릿. 몬스터, 아이템, UI 요소 등을 프리팹으로 만들어 효율적으로 관리합니다.
        
    - **Asset:** 게임에 사용되는 모든 미디어 파일 (이미지, 3D 모델, 사운드, 애니메이션) 및 스크립트를 포함합니다.
        
- **C# 스크립팅:** 게임의 로직은 C# 언어로 작성됩니다.
    
    - **MonoBehaviour:** 게임 오브젝트에 연결하여 동작하는 스크립트의 기본 클래스입니다. `Start()`, `Update()` 등 Unity의 생명주기 메서드를 사용합니다.
        
    - **Coroutine:** 비동기 작업을 효율적으로 처리하는 Unity의 기능입니다. 예를 들어, 애니메이션 지연, 특정 시간 대기 등에 사용됩니다.
        
    - **ScriptableObject:** 데이터를 저장하고 관리하는 데 사용되는 Unity 에셋입니다. 몬스터 정보, 아이템 속성, 퀘스트 데이터 등 게임의 기본 데이터를 에디터에서 쉽게 만들고 관리할 수 있습니다. (서버와 독립적으로 게임 데이터를 구조화하는 데 매우 유용합니다.)
        

### 2. 모바일 특화 개발 고려사항

모바일 기기의 특성을 이해하고 개발에 반영하는 것이 중요합니다.

- **성능 최적화:** 모바일 기기는 PC보다 성능이 제한적이므로, 게임이 원활하게 작동하도록 최적화가 필수입니다.
    
    - **프레임 레이트 (FPS):** 쾌적한 플레이를 위해 30~60 FPS를 목표로 합니다.
        
    - **드로우 콜 (Draw Call) 최적화:** 화면에 오브젝트를 그리는 횟수를 최소화하여 GPU 부담을 줄입니다. (Batching, Sprite Atlas, Occlusion Culling 등)
        
    - **오브젝트 풀링 (Object Pooling):** 자주 생성/파괴되는 오브젝트(예: 발사체, 이펙트)를 미리 만들어 재사용하여 런타임 성능 저하를 방지합니다.
        
    - **메모리 관리:** 불필요한 객체 생성을 줄이고, 에셋 로딩/언로딩을 효율적으로 관리하여 메모리 부족으로 인한 앱 강제 종료를 막습니다.
        
    - **코드 최적화:** `Update()` 함수 내 불필요한 반복문이나 연산을 피하고, 변수 캐싱 등을 통해 성능을 향상시킵니다.
        
    - **텍스처 압축:** 이미지 파일 크기를 최적화하여 메모리 사용량과 로딩 시간을 줄입니다.
        
- **터치 입력 처리:** 마우스 입력이 아닌 터치 입력을 고려하여 UI와 게임 플레이를 설계해야 합니다.
    
    - **Input System:** Unity의 새로운 입력 시스템을 사용하여 터치, 스와이프, 핀치 줌 등 다양한 모바일 제스처를 구현합니다.
        
    - **UGUI Event System:** UI 버튼, 슬라이더 등 UI 요소에 대한 터치 이벤트를 처리합니다.
        
- **다양한 해상도 및 화면 비율 대응:** 수많은 모바일 기기의 화면 크기와 비율에 맞춰 UI와 게임 화면이 항상 보기 좋게 표시되도록 설계해야 합니다.
    
    - **Canvas Scaler:** UI Canvas의 설정을 통해 UI 요소들이 화면 크기에 따라 자동으로 스케일링되도록 합니다. (Scale With Screen Size, Constant Pixel Size 등)
        
    - **Rect Transform의 Anchor/Pivot:** UI 요소들이 화면의 특정 지점(앵커)에 고정되거나 비율에 따라 위치하도록 설정합니다.
        
- **배터리 수명 관리:** 게임이 배터리를 과도하게 소모하지 않도록 프레임 레이트 제한, 불필요한 연산 제거 등을 고려합니다.
    

### 3. 클라이언트 데이터 저장 및 로드 (핵심)

서버 없이 플레이어의 진행 상황과 인벤토리 등을 유지하기 위한 로컬 저장 방식입니다.

- **PlayerPrefs:**
    
    - **용도:** 소량의 간단한 데이터(예: 게임 설정값-음량, 난이도; 마지막 저장 시각) 저장에 최적입니다.
        
    - **제한:** `int`, `float`, `string` 타입만 저장 가능하며, 보안에 취약하여 민감한 데이터는 부적합합니다.
        
- **JSON 파일 저장:**
    
    - **용도:** 몬스터헌터식 게임 데이터(장비 목록, 재료 개수, 몬스터 사냥 횟수, 스토리 진행도 등)와 같이 **복잡하고 구조화된 데이터** 저장에 가장 적합합니다.
        
    - **방법:** C# 객체를 JSON 문자열로 직렬화(Serialize)하여 파일로 저장하고, 게임 로드 시 JSON 파일을 읽어와 다시 C# 객체로 역직렬화(Deserialize)합니다.
        
    - **권장 라이브러리:**
        
        - **Newtonsoft.Json (Json.NET):** `Dictionary` 등 복잡한 C# 컬렉션 및 커스텀 객체 직렬화/역직렬화에 매우 강력하고 유연합니다. Unity Asset Store나 NuGet for Unity를 통해 설치하여 사용합니다. (Unity 기본 `JsonUtility`보다 기능이 훨씬 풍부합니다.)
            
    - **저장 경로:** `Application.persistentDataPath`를 사용하여 앱 데이터가 안전하게 저장되는 플랫폼별 경로에 파일을 저장합니다.
        
- **Binary Serialization (선택 사항):**
    
    - **용도:** JSON보다 파일 크기를 줄이거나, 데이터를 사람이 직접 읽기 어렵게 만들고 싶을 때 고려할 수 있습니다.
        
    - **주의:** `BinaryFormatter`는 보안 취약점으로 인해 Unity에서 더 이상 권장되지 않으므로, **MessagePack-CSharp** 같은 대안 라이브러리를 사용하는 것이 좋습니다.
        

### 4. 빌드 및 출시 준비 (Android & iOS)

플레이스토어(Google Play) 및 앱스토어(Apple App Store)에 출시하기 위한 필수 과정입니다.

- **Player Settings (Unity 내):**
    
    - **Company Name / Product Name:** 앱 개발사 및 앱 이름.
        
    - **Bundle Identifier (Package Name):** 앱을 유일하게 식별하는 ID (`com.yourcompany.yourgame` 형식). 출시 후 변경 불가능하므로 신중하게 결정합니다.
        
    - **Version:** 앱의 버전 번호 (예: `1.0.0`).
        
    - **Build Number / Version Code (Android):** 내부적으로 버전을 구분하는 숫자. 업데이트마다 증가시켜야 합니다.
        
    - **Icon:** 앱 아이콘 설정.
        
    - **Resolution and Presentation:** 화면 방향 (가로/세로), 해상도 설정.
        
    - **Other Settings:** 최소 Android API 레벨, 타겟 API 레벨, 그래픽스 API (Vulkan, OpenGL ES 등) 등 플랫폼별 세부 설정.
        
- **Android 빌드:**
    
    - **JDK (Java Development Kit), Android SDK (Software Development Kit), NDK (Native Development Kit):** Android 앱 빌드에 필요한 외부 개발 도구들입니다. Unity Hub를 통해 쉽게 설치할 수 있습니다.
        
    - **Keystore:** 앱을 서명(Signing)하는 데 사용되는 디지털 인증서 파일입니다. **이 파일은 앱의 신분을 증명하며, 업데이트 시 동일한 Keystore로 서명해야 합니다.** Keystore를 분실하면 앱 업데이트가 불가능하므로 **반드시 안전하게 백업해야 합니다.**
        
    - **APK (Android Application Package) / AAB (Android App Bundle):**
        
        - **APK:** 전통적인 Android 앱 설치 파일 형식입니다.
            
        - **AAB (Android App Bundle):** 구글 플레이에서 권장하는 최신 배포 형식입니다. 사용자의 기기에 최적화된 APK를 생성하여 다운로드 크기를 줄여줍니다. AAB로 빌드하여 제출하는 것이 좋습니다.
            
- **iOS 빌드 (macOS 필요):**
    
    - **Xcode:** iOS 앱 빌드를 위한 Apple의 개발 환경입니다. macOS에서만 실행됩니다.
        
    - **Apple Developer Account:** iOS 앱을 개발하고 앱스토어에 출시하려면 Apple Developer Program에 가입해야 합니다(유료).
        
    - **Provisioning Profile & Certificate:** 앱을 기기에 설치하거나 앱스토어에 제출할 때 필요한 인증서 및 프로비저닝 프로파일을 발급받아야 합니다.
        
- **광고 및 인앱 결제 (선택 사항):**
    
    - **Unity Ads / Google AdMob:** 게임에 광고를 넣어 수익을 얻으려면 해당 SDK를 앱에 통합해야 합니다.
        
    - **Unity IAP (In-App Purchasing) / Google Play Billing / Apple StoreKit:** 게임 내에서 아이템, 재화 등을 판매하려면 각 플랫폼의 인앱 결제 시스템을 통합해야 합니다. 로컬 저장 기반 게임에서도 인앱 결제는 가능합니다.
        

### 5. 출시 이후 업데이트 (플레이스토어/앱스토어 앱 업데이트 방식)

서버 없이 콘텐츠를 업데이트하는 유일한 방법입니다.

- **절차:**
    
    1. Unity 프로젝트에서 새로운 몬스터, 장비, 퀘스트 데이터(스크립트, 에셋 등)를 추가/수정합니다.
        
    2. `Player Settings`에서 앱의 `Version` 및 `Build Number` (또는 `Version Code`)를 이전보다 높게 변경합니다.
        
    3. 새로운 버전의 앱을 Android (AAB) 또는 iOS (Xcode 프로젝트)로 빌드합니다.
        
    4. 구글 플레이 개발자 콘솔 또는 앱스토어 커넥트에 로그인하여 빌드된 새 버전을 업로드하고, 업데이트에 대한 정보를 작성합니다.
        
    5. 앱이 각 스토어의 심사를 통과하면, 사용자들은 플레이스토어나 앱스토어에서 앱 업데이트 알림을 받고 새 버전을 다운로드할 수 있게 됩니다.
        
- **제한점:**
    
    - 사용자가 직접 업데이트해야만 새 콘텐츠를 접할 수 있습니다.
        
    - 작은 변화라도 전체 앱을 다시 다운로드해야 하므로, 업데이트 파일 크기가 클 수 있습니다.
        
    - 스토어 심사 과정 때문에 업데이트가 사용자에게 도달하기까지 시간이 걸릴 수 있습니다.
        

### 6. 기타 중요한 개념

- **버전 관리 (Git):** 개발 과정에서 소스 코드와 에셋의 변경 이력을 관리하고, 여러 개발자와 협업하거나 과거 버전으로 되돌릴 때 필수적입니다. Unity 프로젝트는 바이너리 파일이 많으므로 `.gitignore` 설정을 잘 해야 합니다.
    
- **테스트:** 다양한 기기, 해상도, OS 버전에서 게임을 철저히 테스트해야 합니다. 에뮬레이터/시뮬레이터뿐만 아니라 실제 기기 테스트가 중요합니다.
    
- **디버깅:** Unity Editor의 콘솔, 디버거를 활용하여 스크립트의 오류를 찾고 수정합니다. 모바일 기기에서의 디버깅 방법도 익혀야 합니다.
    
- **구글 플레이 개발자 콘솔 / 앱스토어 커넥트:** 앱 정보를 등록하고, 빌드된 앱을 업로드하며, 출시 상태를 관리하고, 사용자 통계 등을 확인할 수 있는 플랫폼입니다.
    
- **정책 준수:** 각 스토어의 개발자 정책(개인정보 보호, 광고, 인앱 결제 등)을 반드시 숙지하고 준수해야 합니다. 위반 시 앱이 삭제될 수 있습니다.
# 모바일 테스트 방법
### 1. Unity 환경 설정 (최초 1회)

가장 먼저, Unity가 Android 또는 iOS 빌드를 할 수 있도록 환경을 설정해야 합니다.

- **Unity Hub에서 모듈 설치:**
    
    1. **Unity Hub**를 실행합니다.
        
    2. `Installs` 탭으로 이동하여 현재 사용 중인 Unity 버전을 찾습니다.
        
    3. 해당 버전 옆의 톱니바퀴 아이콘(Settings)을 클릭하고 `Add Modules`를 선택합니다.
        
    4. **`Android Build Support`** (Android 빌드를 위해) 와 **`iOS Build Support`** (iOS 빌드를 위해) 모듈을 선택합니다.
        
    5. `Android Build Support`를 선택할 때, 아래쪽에 `Android SDK & NDK Tools` 및 `OpenJDK`도 함께 자동으로 체크되는지 확인하고 설치합니다. (이것들이 Flutter에서 사용하셨던 그 SDK 및 JDK입니다.)
        
    6. `iOS Build Support`를 선택할 경우, Xcode를 macOS에 직접 설치해야 합니다.
        

### 2. Android 에뮬레이터 테스트 방법

Android 에뮬레이터는 실제 기기가 없어도 다양한 기기 환경을 시뮬레이션하여 테스트할 수 있게 해줍니다.

1. **Android Studio 설치 (선택 사항이지만 권장):**
    
    - Unity Hub에서 SDK/NDK/JDK를 설치했더라도, Android Studio를 설치하면 AVD Manager (Android Virtual Device Manager)를 통해 에뮬레이터를 더 쉽게 생성하고 관리할 수 있습니다.
        
    - Android Studio를 설치한 후, `Tools > Device Manager` (또는 AVD Manager)로 이동합니다.
        
2. **새 에뮬레이터 (Virtual Device) 생성:**
    
    - `Create Device` 버튼을 클릭합니다.
        
    - 테스트하고 싶은 **폰 기기 모델**을 선택합니다. (예: Pixel 5)
        
    - 해당 기기에서 실행할 **Android OS 버전** (API Level)을 선택하고 다운로드합니다. (예: Android 13 - API 33)
        
    - 가상 기기의 이름 등을 설정하고 `Finish`를 눌러 생성합니다.
        
3. **에뮬레이터 실행:**
    
    - Device Manager에서 생성된 에뮬레이터 옆의 재생 버튼을 클릭하여 에뮬레이터를 시작합니다.
        
    - 에뮬레이터가 부팅될 때까지 잠시 기다립니다.
        
4. **Unity에서 빌드 및 실행:**
    
    - Unity 프로젝트를 엽니다.
        
    - `File > Build Settings`로 이동합니다.
        
    - **`Android`** 플랫폼을 선택합니다. (선택되어 있지 않다면 `Switch Platform` 클릭)
        
    - `Run Device` 드롭다운 메뉴에서 실행 중인 **에뮬레이터 이름**을 선택합니다.
        
    - **`Build And Run`** 버튼을 클릭합니다.
        
    - Unity가 프로젝트를 Android 앱으로 빌드하고, 실행 중인 에뮬레이터에 앱을 설치한 후 자동으로 실행합니다.
        
    - **팁:** 빌드 시간을 줄이려면 `Development Build`를 체크하고, `Script Debugging`을 체크하면 디버깅이 용이합니다.
        

### 3. Android 실기기 테스트 방법

가장 정확한 테스트 방법입니다.

1. **USB 디버깅 활성화 (기기 설정):**
    
    - Android 스마트폰에서 `설정 > 휴대전화 정보` (또는 `태블릿 정보`)로 이동합니다.
        
    - `빌드 번호`를 여러 번 빠르게 탭 하여 **개발자 옵션**을 활성화합니다.
        
    - `설정 > 시스템 > 개발자 옵션` (또는 `설정` 검색에서 '개발자 옵션' 검색)으로 이동합니다.
        
    - **`USB 디버깅`** 옵션을 켜줍니다.
        
2. **PC에 USB 드라이버 설치:**
    
    - 대부분의 최신 Android 기기는 드라이버가 자동으로 설치되지만, 특정 구형 기기나 브랜드(삼성, LG 등)에 따라서는 제조사 웹사이트에서 USB 드라이버를 수동으로 설치해야 할 수도 있습니다.
        
3. **USB 케이블로 기기 연결:**
    
    - 스마트폰을 PC에 USB 케이블로 연결합니다.
        
    - 폰 화면에 "USB 디버깅 허용" 팝업이 뜨면 "허용"을 선택합니다.
        
4. **Unity에서 빌드 및 실행:**
    
    - `File > Build Settings`로 이동합니다.
        
    - **`Android`** 플랫폼을 선택합니다.
        
    - `Run Device` 드롭다운 메뉴에서 **연결된 실기기 이름**을 선택합니다. (기기 이름이 뜨지 않으면 USB 디버깅 및 드라이버 설치 상태를 다시 확인합니다.)
        
    - **`Build And Run`** 버튼을 클릭합니다.
        
    - Unity가 앱을 빌드하고, 연결된 실기기에 앱을 설치한 후 자동으로 실행합니다.
        

### 4. iOS 실기기 테스트 방법 (macOS + Xcode 필요)

iOS 테스트는 Android보다 설정이 좀 더 복잡하며, **반드시 macOS 컴퓨터가 필요합니다.**

1. **Xcode 설치:**
    
    - App Store에서 최신 버전의 **Xcode**를 다운로드하여 설치합니다.
        
    - Xcode를 처음 실행하면 추가 구성 요소를 설치하는 과정이 있습니다.
        
2. **Apple 개발자 계정 로그인 (Xcode):**
    
    - Xcode를 실행하고 `Xcode > Settings (또는 Preferences) > Accounts`로 이동합니다.
        
    - Apple ID로 로그인합니다. (개인 개발자 계정, 팀 계정 등)
        
3. **기기 등록 및 프로비저닝 프로파일 설정 (Xcode):**
    
    - `Xcode > Window > Devices and Simulators`로 이동합니다.
        
    - 아이폰/아이패드를 USB 케이블로 Mac에 연결하고, 신뢰 여부를 묻는 팝업이 뜨면 "신뢰"를 선택합니다.
        
    - Xcode에서 해당 기기가 인식되면, 개발자 프로파일을 기기에 프로비저닝하여 테스트 앱을 설치할 수 있도록 설정합니다. (무료 계정으로도 제한적인 테스트는 가능)
        
4. **Unity에서 iOS 프로젝트 빌드:**
    
    - Unity 프로젝트를 엽니다.
        
    - `File > Build Settings`로 이동합니다.
        
    - **`iOS`** 플랫폼을 선택합니다. (선택되어 있지 않다면 `Switch Platform` 클릭)
        
    - `Player Settings...` 버튼을 클릭하여 `Other Settings`에서 `Bundle Identifier`를 설정합니다. (`com.yourcompany.yourgame` 형식)
        
    - **`Build`** 버튼을 클릭합니다. (Android와 달리 `Build And Run`이 바로 되지 않습니다.)
        
    - 빌드할 폴더를 선택하고 빌드를 시작합니다. Unity가 해당 폴더에 Xcode 프로젝트를 생성합니다.
        
5. **Xcode에서 기기로 실행:**
    
    - Unity가 생성한 Xcode 프로젝트 파일(`.xcodeproj`)을 Xcode로 엽니다.
        
    - Xcode 왼쪽 상단의 스키마(Scheme) 드롭다운에서 **연결된 iPhone/iPad 기기**를 선택합니다.
        
    - 재생 버튼(Run)을 클릭하여 앱을 빌드하고 기기에 설치한 후 실행합니다.
        
    - **팁:** 처음 빌드 시, 팀(Team) 설정을 해주거나 서명 관련 에러가 발생할 수 있습니다. Xcode의 에러 메시지를 따라 설정을 맞춰주세요.
        

### 5. 디버깅 팁

- **Unity Editor에서 테스트:** 가장 빠르고 기본적인 디버깅 방법입니다. `Debug.Log()`를 사용하여 콘솔에 메시지를 출력하고, `Breakpoints`를 사용하여 코드 실행을 멈추고 변수 값을 확인할 수 있습니다.
    
- **개발자 빌드 (Development Build):** `File > Build Settings`에서 `Development Build`를 체크하고 빌드하면, 앱 실행 중에도 `Debug.Log()` 메시지가 콘솔이나 외부 로그 뷰어(Logcat for Android, Xcode console for iOS)에 출력되어 실제 기기에서의 동작을 확인하기 좋습니다.
    
- **Script Debugging:** `Development Build`와 함께 `Script Debugging`을 체크하면, Unity 에디터의 C# 디버거를 사용하여 실제 기기에서 실행 중인 앱에 연결하여 코드에 브레이크포인트를 걸고 디버깅할 수 있습니다.
    
- **Android Logcat:** Android Studio나 `adb logcat` 명령어를 통해 기기의 전체 로그를 확인할 수 있습니다. 앱 크래시나 에러 메시지를 찾는 데 유용합니다.
    
- **Xcode Console:** iOS 앱을 Xcode에서 실행하면 Xcode의 콘솔에 앱의 로그 메시지(`Debug.Log`)가 출력됩니다.
    

이러한 방법들을 통해 개발 중간중간에 앱을 실제 기기나 에뮬레이터에서 테스트하면서 버그를 찾고 성능을 최적화할 수 있습니다.