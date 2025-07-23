# ◈11 OpenGL Programming◈

DirectX 8.0 까지 OpenGL 과 Direct3D 는 한 때 비슷한 성능으로 경쟁 관계에 있다가 DirectX 9.0, 10, 11 이후에 개인용 컴퓨터 게임 제작에서 잘 사용이 안되다가 Embedded 환경에서 다시 3D 표준 라이브러리로 정착되는 되고
있습니다 . 특히 OpenGL ES 2.0(OpenGL Embedded
System) 버전부터 쉐이더가 지원이 되어 모바일에서도 PC 에서 사용한 쉐이더를 조금
수정해서 쉽게 적용할 수 있는 장점이 있어 앞으로 많이 주목되고 있습니다 .

" 난 개인용 컴퓨터나 XBOX 에서만 게임을 만들 꺼야 ." 라고 결심을 굳히신 분들도 OpenGL 을 공부해보라고 권하고 싶습니다 . 이러한 이유는 첫 번째로 OpenGL 을 잘 알고 있으면 D3D 의 이해가 빠릅니다 . 혹자는 D3D 의 그래픽 파이프라인은 OpenGL 을 베껴서 만들었다고 비평하는 분들도 있는데 이것은 D3D 의
많은 개념들이 이름만 바꾸면 OpenGL 의 개념과 일치하는 부분이 많고 3D 데이터를 처리하는 절차가 OpenGL 과 상당히 유사하기 때문입니다 . 지금까지 대다수의 그래픽 이론과 기술들이 OpenGL 기반으로 작성된
것이 많습니다 . 만약 여러분들이 중견 프로그래머가 되면 출간된 책보다는 학회지 또는 논문들을 통해서
이론과 기술들을 배울 때가 있는데 이 때 OpenGL 을 알고 있는 것이 상당히 도움이 됩니다 . 이렇게 OpenGL 은 D3D 를
공부하는데 끊어진 지식의 연결 고리를 이어주고 채우지 못한 빈 공간을 메워 줄 수 있습니다 .

두 번째로 OpenGL 은 거의 모든 운영 체제에서 지원이 되고 이식성 (Portability) 또한 뛰어 나서 한 번 만든 코드는 다른 플랫폼에서도 거의 변경 없이 사용할 수 있습니다 . 특히 Embedded 환경에서 게임을 제작을 하려면 개인용 컴퓨터에서
만들고 다음으로 모바일에 이식하는 것이 보통입니다 . 이런 방식의 작업에서 OpenGL 은 상당히 장점이 많이 있습니다 . 모바일에서 DirectMobile3D 가 있지만 Windows 시스템에만 지원이
되고 있습니다 . 현재 NDS(Nintendo DS), iPhone 등에서 3D 게임은 OpenGL ES 로 개발하고 있으며 , 국산 게임기 Wiz( 게임파크홀딩스 ) 도 OpenGL 을 지원하고 있습니다 .

수 많은 대한민국의 게임 개발사들이 전부 Direct3D 기반으로
게임을 제작하고 있어서 취업에 OpenGL 이 별 도움이 안될 것이란 생각이 있더라도 이렇게 멋진 그래픽 API 를 한 번도 만져보지 못했다면 3D 게임 프로그래머로서
불행이라 여기고 꼭 , 해보기 바랍니다 .



말이 좀 길어졌는데 이 장에서는 OpenGL 의 모든 내용을 전부 설명하기
보다는 그 동안 우리가 공부한 D3D 순서에 맞추어 필요한 내용만 간추려서 살펴보겠습니다 . 먼저 개인용 컴퓨터에서 OpenGL 을 활용해 보고 다음으로 AMD 사에서 만든 OpenGL ES Emulator 를 이용해서 Embedded 환경에서의 프로그램 방법을 알아보겠습니다 .





11.1 OpenGL

11.1.1 Rendering
Context (RC)

D3D 의 디바이스에 대한 역할에 해당하는 것이 OpenGL 의 Rendering Context(RC: 렌더링 문맥 ) 입니다 . 이 RC 는 3D 렌더링 머신 또는 처리에 대한 모든 것으로 그래픽을 처리하는 Device
Context(DC: 장치 문맥 ) 을 대신해서 렌더링을 담당합니다 . RC 의 생성 방법은 운영 체제마다 다르지만 이 RC 안에서 3D 그래픽 처리에 대한 모든 과정과 프로그래머가 렌더링 머신의 상태를 설정하거나 데이터를 전달하는 모든 방법은
동일합니다 .

윈도우 시스템에서 이 RC 를 만들기 위해서 wgl( 읽을 때 위글 : wiggle) 함수를 이용합니다 . RC 에 대한 위글 함수는 RC 를 생성하는 wglCreateContext(), RC 를 해제하는 wglDeleteContext(), 윈도우의 DC 에 RC 를
설정하는 wglMakeCurrent() 함수가 있습니다 .

하나의 DC 는 여러 RC 를
둘 수 있고 DC 는 이들 RC 중에서 하나를 선택해 그래픽
처리를 맡기게 됩니다 . 이것은 하나의 프로세스 안에서 여러 쓰레드를 이용해 렌더링 하는 것도 가능하다는
것입니다 . 하지만 보통 게임 프로그램에서 RC 는 하나만 만들며 RC 를 생성과 동시에 DC 에 wglMakeCurrent() 함수를 이용해서 지정합니다 .



RC 의 생성 , 렌더링 , 그리고 해제에 대한 순서를 정리하면 다음과 같습니다 .

1. 여러분은 RegisterClass(),
CreateWindow() 함수를 이용해서 윈도우를 생성하고 생성된 윈도우에서 GetDC() 함수를
이용해 DC 를 얻어옵니다 .

2. RC 를 만들기 전에 여러분이 렌더링에 필요한 픽셀 형식에 가장
근접한 것을 DC 에서 찾습니다 .

3. 픽셀 형식을 DC 에
설정하고 RC 를 만듭니다 .

4. OpenGL 함수들을 이용해서 렌더링을 구현합니다 .

5. 프로그램의 종료는 RC 해제 , DC 해제 , 윈도우 해제 순으로 합니다 .



gl01_device.zip 은 RC 의 생성 / 렌더링 / 해제에
대한 예입니다 .



먼저 OpenGL 을 사용하기 위한 라이브러리와 헤더 파일을 다음과
같이 추가합니다 .



#pragma comment ( lib , "OpenGL32.Lib" )

#pragma comment ( lib , "Glu32.lib" )

#pragma comment ( lib , "GlAux.Lib" ) // OpenGL auxiliary library

…

#include <gl/gl.h> //
standard OpenGL include

#include <gl/glu.h> //
OpenGL utilities

#include <gl/GLAux.h> // OpenGL Auxiliary





윈도우를 만들었으면 GetDC() 함수를 이용해 DC 를 생성 얻은 다음 PIXELFORMATDESCRIPTOR 구조체를
이용해서 우리가 필요로 하는 픽셀 형식을 다음과 같이 설정합니다 .



PIXELFORMATDESCRIPTOR pfd = { 0 }; // 반드시 초기화

pfd.nSize      = sizeof ( PIXELFORMATDESCRIPTOR );

pfd.nVersion   = 1 ;

pfd.dwFlags    = PFD_DRAW_TO_WINDOW | PFD_SUPPORT_OPENGL | PFD_DOUBLEBUFFER ;

pfd.iPixelType = PFD_TYPE_RGBA ; // 색상 모드 :
RGBA

pfd.cColorBits = 32 ; // 색상 32 비트

pfd.cDepthBits = 24 ; // 깊이 24 비트

pfd.cStencilBits= 8 ; // 스텐실 8 비트

pfd.iLayerType = PFD_MAIN_PLANE ; // Main Layer



이 구조체는 D3D 의 D3DPRESENT_PARAMETERS 구조체와 비슷합니다 . 여러분이 이 구조체에 대한 코드에서 변경해볼 만한 것은 ColorBit, DepthBit, StencilBit 정도 입니다 . 앞의 PFD 플래그 값 DRAW_TO_WINDOW 은 픽셀 버퍼가 윈도우
혹은 장치의 서피스에 대한 렌더링을 , SUPPORT_OPENGL 는 픽셀 버퍼가 OpenGL 을 지원하는 것을 , DOUBLEBUFFER 픽셀 버퍼의 이중
버퍼링을 의미합니다 .

이렇게 구조체를 채우고 DC 에서 지원이 되는지 다음과 같이 확인합니다 .



UINT PixelFormat = ChoosePixelFormat (m_hDC, &pfd);



ChoosePixelFormat() 함수는 가장 근접한 형태의 포맷을
찾아주며 인덱스를 반환 합니다 . 실패할 경우는 인덱스가 0 입니다 .

다음으로 찾은 인덱스 , PIXELFORMATDESCRIPTOR 구조체
값을 다음과 같이 DC 에 설정합니다 .



SetPixelFormat (m_hDC, PixelFormat,&pfd);



설정이 성공하면 DC 를 통해서 RC 를
생성합니다 .



m_hGlDC = wglCreateContext (m_hDC);



게임에서는 보통 하나의 RC 만 사용하므로 곧바로 DC 에 현재의 렌더링 문맥으로 설정합니다 .



wglMakeCurrent (m_hDC, m_hGlDC);



RC 생성이 끝났습니다 . 렌더링이
제대로 되는지 색상 버퍼를 특정한 색으로 glClearColor() 함수로 지정하고 glClear() 함수로 채웁니다 .



glClearColor ( 0.0f , 0.6f , 0.8f , 1.0f ); // R, G, B, A

glClear ( GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT | GL_STENCIL_BUFFER_BIT );



glClearColor() 함수는 [0, 1] 범위의 색상 값을 Red, Green, Blue, Alpha 순으로 glClear() 함수가 호출되어 색상 버퍼를 특정한 색상으로 채울 때 그 색상을 지정하는 함수 입니다 . 디폴트 색상은 (0,0,0,1) 입니다 .

앞에서 PIXELFORMATDESCRIPTOR 구조체의 값에 깊이 24, 스텐실 8 비트를 사용했기 때문에 깊이와 색상 버퍼도 같이 Clear() 했습니다 . 참고로 Clear 에 대한 디폴트 깊이 (Depth) 값은 1.0 이고 , 스텐실 (Stencil) 은 0 입니다 . 이들을 지정하려면 glClearDepth(), glClearStencil() 함수를 이용합니다 .



렌더링에서 장치 (DC) 에 대해서 전면 버퍼와 후면 버퍼 교체를 SwapBuffers() 함수로 지시합니다 .



SwapBuffers (m_hDC); // 전면 버퍼 후면 버퍼 교체



프로그램을 종료하기 전에 RC, DC, 윈도우 순으로 설정한 자원을
해제합니다 .



wglMakeCurrent (m_hDC, NULL ); // 장치에 대한 렌더링 문맥
설정 해제

wglDeleteContext (m_hGlDC); // 렌더링 문맥 해제

ReleaseDC (m_hWnd, m_hDC); // 윈도우 DC 해제



gl01_device.zip 을
실행하면 다음과 같이 화면 전체에 지정한 색상이 출력되는 것을 볼 수 있습니다 .





<OpenGL Rendering Context 생성 : gl01_device.zip >



gl01_device.zip 을
실행하면 다음과 같이 화면 전체에 지정한 색상이 출력되는 것을 볼 수 있습니다 .



gl01_device.zip 예제는 게임 프로그램에 적합 형태는 아닙니다 . D3D 에서와 마찬가지로 C++ 클래스로 구조화 하는 것이 좋습니다 . gl01_device_c++.zip 예제는 CApplicationGL 에서 윈도우와 RC 에 대한 관리를 담당하고
게임 코드는 CApplicationGL 클래스를 상속 받은 CMain 클래스에서 구현되도록 만든 예입니다 . CMain 클래스의 멤버 함수는 게임 데이터 생성 / 소멸 / 갱신 / 렌더링에 대해서
처리할 수 있도록 Init()/Destroy()/FrameMove()/Render() 함수를 만들어
놓았습니다 .

앞으로 설명한 예제는 모두 gl01_device_c++.zip 을
기반으로 작성되어 있습니다 .





11.1.2 정점과 색상

OpenGL 은 D3D 에
비해서 정점 출력이 상당히 유연하고 자유롭습니다 . D3D 는 정점을 출력하기 위해서 정점 구조체 , 정점 버퍼 , FVF 등을 설정해야 하지만 OpenGL 은 정점 버퍼뿐만 아니라 개별적인 데이터까지 조합해서 렌더링이 가능합니다 .



기본적인 렌더링 방법은 다음과 같습니다 .



glBegin ( " 프리미티브 타입 " );

glVertex { 2 | 3 | 4 }{ i | s | f | d }[ v ]({ " 위치 x,  y , z" | " 위치 배열 " });

glEnd ();



glBegin()/glEnd() 함수는 정점 렌더링의 시작과 끝을
알립니다 . 이것은 D3D 의 DrawPrimitive() 함수를 호출 하는 것과 비슷합니다 . glBegin() 함수의
인수는 다음의 프리미티브 중에서 하나를 선택합니다 .



GL_POINTS: 점 리스트

GL_LINES: 2 개의 정점 단위로 선을 구성

GL_LINE_LOOP: 모든 정점이 순서대로 연결되어 폐곡선 형성

GL_LINE_STRIP: 연결된 선 형성

GL_TRIANGLES: 3 개의 정점 단위로 삼각형 구성

GL_TRIANGLE_STRIP: 연결된 삼각형 형성 . 삼각형 감는 방법은 반 시계 , 시계 , 반 시계 ,… 를 반복

GL_TRIANGLE_FAN: 부채꼴 모양으로 삼각형을 형성 .

GL_QUADS: 4 개의 정점을 한 단위로 사각형 형성

GL_QUAD_STRIP: 연결된 사각형 형성

GL_POLYGON : 임의의 다각형 형성



이 중에서 가장 많이 사용되는 것은 GL_TRIANGLES, GL_LINES,
GL_POINTS 입니다 . 만약 삼각형에 대해서 인덱스 버퍼를 사용하게 되면 D3D 와 마찬가지로 프리미티브 타입은 GL_TRIANGLES 이 됩니다 . 인덱스 버퍼를 사용하는 예는 정점 버퍼를 이용할 때 다시 설명하겠습니다 .





<OpenGL Geometric Primitive Type>



glVertex…() 함수는 RC 에
전달하는 정점 데이터와 형식을 지정하는 함수 입니다 . 처음에 나오는 숫자는 데이터의 차원입니다 . 다음의 i, s, f, d 는 각각 int, short int, float, double 형을 말합니다 . 마지막 v 는 인수에 전달할 값이 개별적인 값이 아닌 배열 형태일 때 사용합니다 .

예를 들어 만약 2 차원 int 형
데이터 x, y 를 렌더링 하게 되면 glVertex2i( x, y) 형식으로
작성합니다 . 가장 많이 사용되는 함수는 3 차원에 대해서 glVertex3f(x, y, z) 또는 glVertex3v("float 형 배열 ") 입니다 .



다음은 화면에 삼각형 한 개를 그리는 예입니다 .



glBegin ( GL_TRIANGLES );

glVertex3f ( - 1 , - 1 , 0 );

glVertex3f ( 1 , - 1 , 0 );

glVertex3f ( 0 , 1 , 0 );

glEnd ();





< 삼각형 : gl03_triangle.zip >



앞의 삼각형에 색상을 추가 하고 싶으면 glColor*() 함수 이용합니다 . 다음의 예는 앞의 삼각형에 전체를 붉은 색으로 지정해서 렌더링 하는 예 입니다 .



glColor4f ( 1 , 0 , 0 , 1 );

glBegin ( GL_TRIANGLES );

glVertex3f ( - 1 , - 1 , 0 );

glVertex3f ( 1 , - 1 , 0 );

glVertex3f ( 0 , 1 , 0 );

glEnd ();





< 삼각형 - 색상 : gl03_triangle.zip >



OpenGL 의 어떤 함수는 렌더링 머신의 상태 지정과 비슷해서 새로운
값이 오기 전까지 이전 값을 계속 유지하는 경향이 있습니다 . glColor*() 는 전체 정점 또는 개별
정점들에 대해서 색상을 지정합니다 . 만약 정점에 대해서 개별적인 색상을 지정하려면 다음과 같이 정점의
앞에 glColor*() 함수를 지정합니다 .



glBegin ( GL_TRIANGLE_FAN );

glColor4f ( 1 , 0 , 0 , 1 ); glVertex3f ( - 1 . 0F , - 1 . 0F , 0 );

glColor4f ( 0 , 1 , 0 , 1 ); glVertex3f ( 1 . 0F , - 1 . 0F , 0 );

glColor4f ( 0 , 0 , 1 , 1 ); glVertex3f ( 1 . 0F , 1 . 0F , 0 );

glColor4f ( 1 , 0 , 1 , 1 ); glVertex3f ( - 1 . 0F , 1 . 0F , 0 );

glEnd ();





< 사각형 : gl03_triangle.zip >



glColor*() 함수 이외에 glBegin()/glEnd() 함수 안에서 가장 많이 사용하는 함수는 정점의 법선 벡터를 지정하는 glNormal*(), 텍스처
좌표를 지정하는 glTexCoord*(), glMultiTexCoord*ARB() 함수 들이고 , 이 이외에 glIndex*(), glEvalCoord*(),
glEvalPoint*(), glMaterial*(), glEdgeFlag*(), glCallList*() 함수 등도 종종 사용 됩니다 .





11.1.3 변환 (Transform)

변환이라는 것은 그래픽 파이프라인 안에서 정점의 위치를 행렬과 곱해 새로운 위치를 만드는 과정입니다 . D3D 의 정점은 모델 좌표 à 월드 좌표계 à 뷰 (View) 좌표계 à 정규 좌표계 à 장치 좌표계로
변환하고 이를 위해 우리는 각각의 좌표계에 대한 행렬을 SetTransform() 함수를 이용해 지정해왔습니다 . OpenGL 이 변환에서 D3D 와 차이는 월드 좌표계 , 뷰 좌표계 변환을 결합한 모델 - 뷰 (Model-View) 좌표계 변환을 수행합니다 . 그리고 D3D 는 정규
변환을 거치면 정점의 위치 x, y 는 [-1, 1], z 는 [0, 1] 이지만 OpenGL 은 정규 변환 후 x, y, z 는 전부 [-1, 1] 의 범위를 갖습니다 .

이전의 삼각형과 사각형 예제들은 이런 점을 이용해서 정점의 위치 값을 -1, 0,
1 에서 선택해서 만든 것입니다 .



OpenGL 은 D3D 와
다르게 모든 것이 렌더링 머신의 상태를 함수로 결정할 수 있습니다 . 만약 모델 _ 뷰 행렬을 RC 에 적용하려면 여러분은 다음과 같은 코드를 작성해야
합니다 .



float mtViw[ 16 ]={ 0 }; // Model - View Matrix



// Setup Model-View Matrix

…

glMatrixMode ( GL_MODELVIEW );

glLoadIdentity ();

glLoadMatrixf (mtViw);

glMatrixMode() 함수는 D3D 의 SetTransform() 함수와 같이 렌더링 머신의 변환 행렬을 지정하는 함수이며 모델 - 뷰 변환에 대한 인수는 GL_MODELVIEW, 투영 변환에 대한
인수는 GL_PROJECTION 입니다 .

glLoadIdentity() 함수는 렌더링 머신의 현재 지정된 변환의
행렬을 단위 행렬 ( 항등 행렬 ) 로 초기화 하는 함수입니다 .

glLoadMatrix*() 함수는 머신의 행렬 스택에서 최상 위
행렬의 값을 변경하는 함수로 앞의 코드에서는 최상위 행렬이 Model-View 변환 행렬입니다 .



만약 여러분들이 직접 행렬 값을 만들 때 주의할 것은 OpenGL 의
모든 연산은 오른손 좌표 시스템을 기준으로 진행됩니다 . 그런데 glLoadMatrix*() 함수가 요구하는 것은 행 우선이 아닌 열 우선 행렬 입니다 .

이것은 연산의 속도를 빠르게 하기 위해서 연산 처리가 " 벡터 ' = 행렬 * 벡터 " 가
아닌 D3D 처럼 내부에서 " 벡터 '= 벡터 * 행렬 " 을
수행하기 때문에 열 우선으로 값을 채워야 합니다 . 이런 점이 있어 D3D SDK 의 수학 함수를 이용할 수 있습니다 .



glLoadMatrix*() 함수를 사용하지 않고 모델 - 뷰 변환 행렬을 지정하려면 OpenGL Utility 함수 gluLookAt() 를 이용합니다 .



다음은 카메라 위치가 (0, 0, 10), 보고 있는 지점이 (0, 0, 0), Up 벡터가 (0, 1, 0) 일 때 렌더링 머신의
모델 - 뷰 행렬 값을 바꾸는 예입니다 .



glMatrixMode ( GL_MODELVIEW );

glLoadIdentity ();

gluLookAt ( 0 , 0 , 10 , 0 , 0 , 0 , 0 , 1 , 0 );



비슷하게 머신의 투영 행렬을 투시 투영 (Perspective
Projection) 으로 바꿀 때는 gluPerspective() 함수를 다음과 같이 사용합니다 .



glMatrixMode ( GL_PROJECTION ); // Projection 선택

glLoadIdentity ();

gluPerspective ( 45 , float (m_ScnW)/ float (m_ScnH), 1 .f, 5000 .f);



D3D 에서 사용하는 각도는 Radian 이지만 OpenGL 에서 사용하는 모든 각도는 Degree 입니다 . gluPerspective() 함수는 인수로 FOV(Field of View), Aspect Ratio, Near Value, Far Value 값을 받는데
이 때 FOV 값은 Degree 이어야 합니다 .



만약 뷰포트 값을 바꾸고 싶을 때는 glViewport() 함수를
사용합니다 . 뷰 포트는 지정하지 않으면 DC 의 영역이 됩니다 .



D3D 는 디바이스의 함수를 호출해서 상태 값을 변경하기 때문에 함수의
순서가 바뀌어도 상관 없었습니다 . 하지만 OpenGL 은 함수의
호출 순서가 중요할 때가 있습니다 . 예를 들어 다음과 같이 MODEL_VIEW 를 먼저 설정하고 , PROJECTION 을 나중에 할 경우에 MODEL_VIEW 를 지정하지 않고 그냥 glLoadMatrix*() 함수를
호출할 경우 PROJECTION 행렬이 바뀌게 됩니다 .



glMatrixMode ( GL_MOVELVIEW ); // Model View 선택

…

glMatrixMode ( GL_PROJECTION ); // Projection 선택

…

glLoadMatrixf (…);



원래의 의도는 MODEL_VIEW 행렬을 변경하려고 한 것인데 PROJECTION 행렬을 바꾸게 되었습니다 . 이런 실수를 안 하려면 "OpenGL 의 상태는 스택처럼 마지막에 지정된 것이 적용된다 " 라는
것을 명심하고 파이프라인에서 처리되는 순서를 거꾸로 적용해서 다음의 예와 같이 작성하는 것이 좋습니다 .



glViewport ( 0 , 0 , m_ScnW, m_ScnH ); // 뷰포트 설정



glMatrixMode ( GL_PROJECTION ); // Projection 선택

gluPerspective ( 45 , float (m_ScnW)/ float (m_ScnH), 1.0f , 5000.0f );



glMatrixMode ( GL_MODELVIEW ); // Model View 선택

gluLookAt ( 0 , 0 , 10 , 0 , 0 , 0 , 0 , 1 , 0 );



물론 , 항상 {glMatrixMode(),
glLoadMatrix*()} 로 코드를 작성하는 습관을 들이는 것이 가장 좋은 방법입니다 .





11.1.4 행렬 스택 (Matrix Stack)

앞서 OpenGL 은 월드 변환과 뷰 변환이 동시에 진행된다고 했습니다 . 월드 변환과 뷰 변환을 따로 지정할 수 없는 것은 여간 불편한 일이 아닐 수 없습니다 . 이 때는 모델 - 뷰 변환 행렬은 카메라의 뷰 행렬로 지정하고 각 모델의
월드 변환은 행렬 스택을 이용해서 처리하는 것입니다 . 행렬 스택의 종류는 모델 - 뷰 행렬 스택 , 투영 행렬 스택 , 텍스처
행렬 스택 3 종류가 있습니다 .



가장 많이 사용하는 모델 - 뷰 행렬 스택의 예를 들어 봅시다 .

만약 여러분이 지역 좌표계로 설정되어 있는 모델을 월드 공간으로 이동하기 위해 다음과 같은 코드를 작성할 수
있습니다 .



glMatrixMode ( GL_MODELVIEW );

glTranslatef (- 2 , 1 , 3 );



glTranslate*() 함수는 크기 , 회전 , 이동 변환 중에서 이동 변환을 위해서 사용하는 함수이며 지정된
렌더링 머신의 행렬을 이동 변환 행렬을 곱한 결과로 교체하는 함수입니다 .

앞의 코드는 결국 모델 - 뷰 행렬을 다음과 같은 행렬 값으로 변경한
것과 다름이 없습니다 .







이것은 하나의 모델과 하나의 카메라에서는 별 문제 없지만 여러 모델의 경우에 각각의 모델에 대해서 항상 gluLookAt() 함수 또는 glLoadMatrix*() 함수를 호출해서
렌더링 머신의 모델 - 뷰 행렬을 지정해야 우리가 원하는 형태인 월드 변환 à 뷰 변환을
만들 수 있습니다 .



그런데 행렬 스택을 이용하면 매번 모델 - 뷰 행렬을 지정 안 해도 됩니다 . 행렬 스택은 현재의 행렬을 스택의 top 에 일시적으로 복사해 놓고
사용하는 것입니다 . 행렬 스택의 top 에 현재의 행렬을 복사하는
방법은 glPushMatrix() 함수를 이용하고 해제는 glPopMatrix() 를
이용합니다 .



예를 들어 다음과 같은 코드를 작성했다고 합시다 .



glMatrixMode ( GL_MODELVIEW ); // Model View 선택

glPushMatrix ();

glTranslatef (x, y, z);

glBegin ( GL_TRIANGLES );

…

glEnd ();

glPopMatrix ();



glPushMatrix() 함수는 모델 - 뷰 행렬을 스택에 복사합니다 . 다음으로 glTranslate*() 함수는 이동 변환 행렬을 만든 다음에 스택에 복사된 모델 - 뷰 행렬 곱합니다 . 렌더링 머신은 이 곱한 행렬을 모델 - 뷰 행렬로 사용해서 정점을 변환합니다 .

glPopMatrix() 함수를 이용해서 이전의 모델 - 뷰 행렬로 되돌아 옵니다 .



모델이 지역 좌표계에서 먼저 크기 변환하고 , 회전 변환한 다음에 월드
좌표계의 위치로 이동하는 경우에 여러분은 다음과 같이 작성해야 합니다 .



glPushMatrix ();

glTranslate* (…);

glRotate* (…);

glScale* (…);

glBegin ();

…

glEnd ();

glPopMatrix ();



D3DD 에서 모델의 월드 변환 행렬을 만들 때 크기 행렬 * 회전 행렬 * 이동 행렬 순으로 곱했던 기억이 있습니다 . 또한 앞서 OpenGL 의 렌더링 머신은 스택처럼 동작한다는 것을
생각한다면 정점을 그리는 glBegin() 함수에서 가장 가까운 위치에 크기 변환 행렬이 있어야 하고 , 그 다음으로 회전 , 이동 순서대로 코드를 작성해야 원하는 형태로
출력이 됩니다 .



glRotate*() 함수는 회전 변환 행렬을 만들고 지정된 행렬에
곱셈을 합니다 . glScale*() 함수는 비슷하게 크기 변환 행렬을 만들고 지정된 행렬에 곱셈을 합니다 .



앞의 코드는 다음과 같이 서술할 수 있습니다 .



모델 - 뷰 행렬 ' = 이동
행렬 * 모델 - 뷰 행렬 (glTranslate)

모델 - 뷰 행렬 '' = 회전
행렬 * 모델 - 뷰 행렬 '           (glRotate)

모델 - 뷰 행렬 ''' = 크기
변환 행렬 * 모델 - 뷰 행렬 ''    (glScale)



이것은 결국 우리가 원하는 다음과 같은 연산과 동일하게 됩니다 .



새로운 모델 - 뷰 행렬 = 크기
변환 행렬 * 회전 행렬 * 이동 행렬 * 모델 - 뷰 행렬



이렇게 OpenGL 이 행렬의 연산을 열 중심으로 계산한다면 우리는 DXSDK 의 수학 함수를 사용해서 OpenGL 를 좀 더 편리하게 사용할
수 있습니다 .

OpenGL 의 변환 행렬에 대해서 임의의 행렬을 곱한 결과를 새로운
변환 행렬로 지정하는 함수는 glMultMatrix*() 입니다 . 이
함수를 사용해서 앞의 glTranslate*() 는 D3DXMatrixTranslate() 함수로 이동 행렬을 먼저 만들고 이동 행렬을 glMultMatrix*() 함수로 곱합니다 . glRotate*() 는 D3DXMatrixRotationAxis() 함수로 회전 행렬을 만들고 이 회전 행렬을 glMultMatrix*() 함수를 호출에서
행렬을 곱하도록 합니다 . glScale *() 는 D3DXMatrixScaling() 함수로 크기 변환 행렬을 만들고 같은 방법으로 glMultMatrix*() 함수를 호출합니다 .



gl04_transform1.zip 의 CMain::Render() 함수에서 다음과 같이 직접 OpenGL 의
렌더링 머신의 행렬을 설정하는 예와 DXSDK 함수를 이용해서 행렬을 구하고 이를 적용하는 예가 있습니다 .



glPushMatrix ();

glTranslatef (- 2 , 0 , 0 );

glRotatef (fAngle, 0 , 1 , 0 );

glBegin ( GL_TRIANGLES );

…

glEnd ();

glPopMatrix ();



glPushMatrix ();

D3DXMatrixTranslation (&mtTrn, 2 , 0 , 0 );

D3DXMatrixRotationAxis (&mtRot,
& D3DXVECTOR3 ( 0 , 1 , 0 ), D3DXToRadian (fAngle));

glMultMatrixf (( FLOAT *)mtTrn);

glMultMatrixf (( FLOAT *)mtRot);



glBegin ( GL_TRIANGLES );

…

glEnd ();

glPopMatrix ();





< 변환 : gl04_transform1.zip >



그런데 왜 우리는 OpenGL 에서 지원되는 함수를 사용하지 않고 DXSDK 함수를 사용해서 행렬을 만들고 이를 적용하는 이와 같은 복잡한 과정을 하는 것일까요 ?

단순히 렌더링만 하면 이와 같은 과정은 전혀 필요 없습니다 . 그런데
게임 프로그램 안의 오브젝트 충돌을 생각해 봅시다 . 결국 월드 행렬을 따로 가지고 있어야 합니다 . 그리고 3D 모델의 애니메이션을 생각해봅시다 . 애니메이션 데이터가 행렬로 되어 있으면 glRotate*(),
glTranslate*() 함수에 맞게 회전과 이동 추출을 따로 구해서 작성하게 되어 일반적인 코드가 아닌 하나의 경우에만 사용하는
특별한 코드를 만들어야 합니다 . 쉐이더 , 카메라 , 기타 앞으로 닥칠 여러 상황에 대해서 생각한다면 렌더링과 데이터 갱신이 분리될 수 있도록 작업해야 하고 이것의
최초 모습은 앞서 보인 코드와 비슷할 것입니다 .

DXSDK 의 수학 함수를 사용한 것은 현재 우리는 수학 함수를 따로
만들어 사용하고 있지 않기 때문입니다 . 만약 여러분들이 작성한 수학 함수들이 있다면 그것으로 바꾸면
됩니다 .



gl04_transform2_dxmath.zip 은 OpenGL 의 glRotate*(), glTranslate*() 함수
등을 사용하지 않고 수학 함수를 사용해서 월드 행렬을 구성한 다음 렌더링 예제 입니다 . CMcScene::FrameMove() 함수에서 다음과 같이 월드 행렬을 구성하고 CMcScene::DrawModel() 함수에서
행렬 스택 설정 안에서 모델 - 뷰 행렬과 곱하고 있음을 볼 수 있습니다 .



INT CMcScene :: FrameMove ()

…

D3DXMATRIX mtScl1;

D3DXMATRIX mtRot1;

D3DXMATRIX mtTrn1;

D3DXMatrixScaling (&mtScl1, 3 . 0f , 3 . 0f , 3 . 0f );

D3DXMatrixRotationAxis (&mtRot1,
& D3DXVECTOR3 ( 0 , 0 , 1 ), D3DXToRadian (fAngle* 1 . 2f ));

D3DXMatrixTranslation (&mtTrn1,
- 5 , 0 , 0 );

m_mtObj1
= mtScl1 * mtRot1 * mtTrn1;

…



void CMcScene :: DrawModel ( D3DXMATRIX * mtWorld)

{

glPushMatrix ();

glMultMatrixf (( FLOAT *)mtWorld);

glBegin ( GL_TRIANGLE_FAN );

…

glEnd ();

glPopMatrix ();

}



< 행렬 스택과 모델의 월드 행렬 : gl04_transform2_dxmath.zip >





11.1.5 조명 (Lighting)

OpenGL 과 D3D 에서
사용되는 조명의 공식과 색상 처리는 같기 때문에 이론적인 부분은 넘어가겠습니다 .

약간의 주의 사항은 방향 광원에 대해서 방향은 빛의 위치에서가 아니라 다음 그림처럼 정점의 위치에서 빛의 방향이
되어 원래 생각한 방향에 "-" 를 붙여야 합니다 .





<OpenGL 에서 조명 계산에 관련한 벡터 >



조명을 효과를 만들기 위해서 조명의 위치 , 색상 , 재질의 색상 등이 필요합니다 . 조명의 위치는 x, y, z, w 값을 사용하는데 w=1 이면 조명이 점 광원 (Point Light) 임을 , w=0 이면 조명이 방향 광원 (Directional Light) 를 의미합니다 . Spot Light 는
따로 설정합니다 .

또한 OpenGL 은 하나의 렌더링 오브젝트에 조명을 8 개까지 (LIGHT0 ~ LIGHT7) 설정할 수 있습니다 .



방향 광원을 설정하려면 다음과 같이 조명의 방향과 색상을 지정합니다 .



D3DXVECTOR4 LgtPos0 (- 1 . 0f ,- 1 . 0f , - 1 . 0f , 0 . 0f ); // 조명 방향

D3DXCOLOR LgtCol0 ( 1 . 0f , 0 . 0f , 0 . 0f , 1 . 0f ); // 조명 색상



이 조명을 0 번 인덱스 Lighting 으로 설정하기 위해서 우리는 glLightfv() 함수를 다음과 같이 사용합니다 . 램버트 확산에 대해서 OpenGL 의 조명은 Diffuse Lighting 으로 구현되어 있습니다 . Diffuse
Lighting 에 대해서 다음과 같이 대해서 설정합니다 .



glLightfv ( GL_LIGHT0 , GL_DIFFUSE ,  ( GLfloat *)LgtCol0); // 0 번 조명 Diffuse 색상 설정

glLightfv ( GL_LIGHT0 , GL_POSITION , ( GLfloat *)LgtPos0); // 0 번 조명 ( 방향 ) 설정



glLightfv() 함수의 두 번째 인수는 조명을 구성하는 Ambient, Diffuse, Specular 등의 색상과 조명의 위치 , 감쇠도 , CUTOFF 등을 지정하는 인수입니다 .



조명의 설정이 끝나면 반사에 대한 오브젝트의 재질을 설정합니다 . 재질은
조명의 색상과 연산이 필요하기 때문에 색상 값을 가지고 다음과 같이 설정합니다 .



D3DXCOLOR MtlCol ( 1 . 0f , 1 . 0f , 1 . 0f , 1 . 0f ); // 재질 색상

glColorMaterial ( GL_FRONT , GL_AMBIENT_AND_DIFFUSE );

glMaterialfv ( GL_FRONT , GL_DIFFUSE , ( GLfloat *)MtlCol);



정점을 그리기 전에 전체 조명과 각각 설정한 조명 , 그리고 재질을
활성화 합니다 .



glEnable ( GL_LIGHTING );

glEnable ( GL_LIGHT0 );

…

glEnable ( GL_COLOR_MATERIAL );



모델의 렌더링 하기 전에 활성화한 GL_COLOR_MATERIAL 값은
색상 추적 (Color Tracking) 으로 오브젝트가 색상을 가질 때 ( 즉 , glColor*() 함수가 호출될 때 ) 자동으로 지정한 색이 재질 속성들로 적용되도록 합니다 . 이 값은
디폴트로 비활성화 되어 있습니다 .



마지막으로 컬링을 통해서 뒷면을 그리지 못하게 하고 , 깊이 테스트를
활성화 합니다 . 다각형을 반시계 방향 (CCW: Counter
Clock Wise) 로 그리도록 한 다음 모델을 그립니다 .



glEnable ( GL_CULL_FACE );

glEnable ( GL_DEPTH_TEST );

glFrontFace ( GL_CCW );

…

DrawModel (&m_mtObj);





<Lighting: Flat-shading,
Gouraud-shading>



Highlight 를 만드는 Specular
Lighting 은 조명에서 가장 매력적인 효과입니다 . OpenGL 의 Specular Lighting 은 퐁 (Phone) 반사를 이용합니다 . 이 효과를 구현하는 방법은 앞서 보인 Diffuse Lighting 과
유사합니다 . 만약 두 개의 Specular 조명을 사용할
때는 다음과 같이 조명과 재질에 대한 위치 , 색상 등을 설정합니다 .



D3DXVECTOR4 LgtPos0 ( 1 . 0f , 0 . 5f , 0 . 5f , 0 . 0f ); // 조명 방향 0

D3DXCOLOR LgtSpc0 ( 0 . 0f , 1 . 4f , 3 . 0f , 1 . 0f ); // 조명 색상 0

D3DXVECTOR4 LgtPos1 (- 1 . 0f ,- 0 . 5f , - 0 . 5f , 0 . 0f ); // 조명 방향 1

D3DXCOLOR LgtSpc1 ( 0 . 9f , 0 . 4f , 0 . 4f , 1 . 0f ); // 조명 색상 1

D3DXCOLOR MtlColS ( 0 . 6f , 0 . 6f , 0 . 6f , 1 . 0f ); // 재질 색상



GL_SPECULAR 는
현재 설정하는 조명이 Specular Lighting 임을 나타내며 High Light 를 만들어 냅니다 .



glLightfv ( GL_LIGHT0 , GL_POSITION , ( GLfloat *)LgtPos0); // 0 번 조명 위치 ( 방향 ) 설정

glLightfv ( GL_LIGHT0 , GL_SPECULAR , ( GLfloat *)LgtSpc0); // 0 번 조명 Specular 색상 설정

glLightfv ( GL_LIGHT1 , GL_POSITION , ( GLfloat *)LgtPos1); // 1 번 조명 위치 ( 방향 ) 설정

glLightfv ( GL_LIGHT1 , GL_SPECULAR , ( GLfloat *)LgtSpc1); // 1 번 조명 Specular 색상 설정



Diffuse
Lighting 과 비슷하게 재질에서도 조명과 곱셈 연산을 하는 Specular Lighting 의
색상을 적용합니다 .



glMaterialfv ( GL_FRONT , GL_SPECULAR ,( GLfloat *)MtlColS);



Specular
Lighting 의 반사 세기 ( 퐁 반사 효과 ) 는
다음과 같이 결정 된다고 했습니다 .



, : 시선
벡터 , : 반사 벡터 ,

이 Power 값은 OpenGL 에서는 Shininess 라 합니다 . 이 값은 재질에서 다음과 같이 설정합니다 .



glMaterialf ( GL_FRONT , GL_SHININESS , 90 . 0F );



Specular 효과에 대해서 조명과 재질의 설정이 끝났습니다 . gl05_lighting_specular.zip 을
실행하면 다음과 같이 High Light 2 개가 만들어진 화면을 볼 수 있습니다 . 이 예제는 Specular 이외에 Diffuse Light 도 같이 적용했습니다 .





<Specular-Diffuse Lighting: gl05_lighting_specular.zip >





11.1.6 텍스처

변환 , 조명 , 텍스처는 3D 프로그램에서 가장 기초적인 부분입니다 . OpenGL 은 오른손
좌표계를 사용하기 때문에 다음과 같은 텍스처 좌표계 (ST- 좌표계 ) 를
사용합니다 .





<OpenGL, Max 의 ST 좌표계 >



<D3D 의 UV 좌표계 >



OpenGL 도 D3D 와
마찬가지로 텍스처 처리에 대한 Addressing, Filtering, Multi-texturing 이
있습니다 .



만약 하나의 단계 대한 텍스처 매핑에서 OpenGL 은 기본 함수를
이용해서 처리할 수 있습니다 . 그런데 여러 단계의 텍스처를 처리하는 다중 텍스처 처리 (Multi-Texturing) 는 OpenGL ARB(Architectural
Review Board) 에서 정의한 함수를 질의 (Query) 를 통해서 얻어와 사용합니다 .

먼저 하나의 단계에서 처리되는 텍스처 매핑을 살펴 봅시다 .



OpenGL 은 텍스처를 " 텍스처
이름 " 으로 구분해서 사용하는데 먼저 " 텍스처
이름 " 을 만들고 이 이름에 픽셀을 결합해야 합니다 . " 텍스처
이름 " 은 unsigned int 형으로 정의되어
있고 glGenTextures() 함수를 사용해서 " 텍스처
이름 " 을 생성합니다 .



GLuint m_nTex; // 텍스처 이름

glGenTextures ( 1 ,&m_nTex);



해제는 glDeleteTextures() 함수를 이용합니다 .



glDeleteTextures ( 1 , &m_nTex);



이 " 텍스처
이름에 " 여러분은 픽셀 데이터를 연결 (Binding) 하기
위해서 glBindTexture() 함수를 먼저 호출하고 다음과 같이 glTexImage2D() 함수를 호출 합니다 .



glBindTexture ( GL_TEXTURE_2D ,
m_nTex);

glTexImage2D ( GL_TEXTURE_2D , 0 , 4 , nImgW, nImgH, 0 , GL_RGBA , GL_UNSIGNED_BYTE ,
pPxl);



glBindTexture() 함수는 두 번째 인수의 값을 0 으로 지정할 때까지 이후 프로세스에 계속 유효하며 이 때
지정된 상태들은 " 텍스처 이름 " 과 렌더링
처리에 영향을 줍니다 .



glTexImag2D() 함수는 glBindTexture() 함수에서 지정한 " 텍스처 이름 " 에 픽셀 데이터를 로드 하는 함수 입니다 . 이 함수의 세
번째 인수는 색상을 구성하는 컴포넌트로 1,2,3,4 중에 선택하거나 색상이 24 비트 RGB 이면 3 또는 GL_RGB 를 , 알파가 있는 32 비트
색상의 경우 4 또는 GL_RGBA 를 사용합니다 . 게임 프로그램에서는 GL_RGBA 혹은 4 를 가장 많이 사용합니다 .

네 번째 , 다섯
번째 인수는 각각 이미지의 너비와 높이인데 문서에는 2 의 승수 2 M 을 사용하라고 하지만 그냥 이미지 너비와 폭을 넣어도 상관 없습니다 . 7 번째 인수는 픽셀
데이터 포맷으로 세 번째 인수로 지정한 형식에 맞추어야 하며 GL_RGB, GL_RGBA, GL_LUMINANCE 를
가장 많이 사용합니다 . 8 번째 인수는 픽셀을 구성하고 있는 성분의 형식입니다 . 게임 프로그램에서는 32 비트 RGBA 를 가장 많이 사용하기 때문에 GL_UNSIGNED_BYTE 값을 주로 사용합니다 .



만약 밉맵이 필요하다면 glBindTexture() 함수 호출 후에 gluBuild2DMipmaps () 함수를 사용해서 자동으로 밉맵을 구성합니다 .



gluBuild2DMipmaps ( GL_TEXTURE_2D , 4 , nImgW, nImgH, GL_RGBA , GL_UNSIGNED_BYTE ,
pPxl);



텍스처에 픽셀을 로드하고 밉맵까지 만들었으면 바인딩을 해제합니다 .



glBindTexture ( GL_TEXTURE_2D , 0 );



픽셀이 텍스처에 로드 되었으므로 사용한 픽셀도 해제 합니다 .



delete pPxl;



" 텍스처 이름 " 을
해제하면 내부에서 로드 한 픽셀은 자동으로 소멸됩니다 .



렌더링은 glEnable() 함수호출로 시작합니다 . OpenGL 은 1 차원에서 3 차원
텍스처까지 사용할 수 있지만 2 차원 텍스처를 주로 사용하고 있기 때문에 대부분 이 함수의 인수 값은 GL_TEXTURE_2D 입니다 . 다음으로 현재의 렌더링에
텍스처 이름을 사용하기 위해서 glBindTexture() 함수를 사용합니다 .



glEnable ( GL_TEXTURE_2D );

glBindTexture ( GL_TEXTURE_2D , m_nTex);



텍스처 샘플링에 대한 필터링 (Filtering) 과 어드레싱 (Addressing) 은 glTexParameteri() 함수를 사용합니다 .



glTexParameteri ( GL_TEXTURE_2D , GL_TEXTURE_MAG_FILTER , GL_NEAREST );

glTexParameteri ( GL_TEXTURE_2D , GL_TEXTURE_MIN_FILTER , GL_NEAREST );



glTexParameteri ( GL_TEXTURE_2D , GL_TEXTURE_WRAP_S , GL_REPEAT );

glTexParameteri ( GL_TEXTURE_2D , GL_TEXTURE_WRAP_T , GL_REPEAT );



모델을 장면에 그리고 나서 현재의 " 텍스처 이름 " 0 으로 설정합니다 . 이렇게 하면 다른 처리 과정에서 현재
사용한 텍스처의 영향을 안 받습니다 .



DrawModel();

glBindTexture ( GL_TEXTURE_2D , 0 );



마지막으로 렌더링 머신의 텍스처 사용을 해제 합니다 .



glDisable ( GL_TEXTURE_2D );



gl06_tex1_single.zip 은
텍스 매핑에 대한 예제입니다 . 키보드의 'N' 키와 'R' 키를 눌러 보면 텍스처 필터링과 어드레싱 적용에 대해서 출력 변화를 볼 수 있습니다 .





< 텍스처 매핑 :
Addressing-Repeat, Clamp>



gl06_tex1_single.zip 은 BMP, TGA, PNG 파일에서 픽셀을 읽어오는 클래스 CGLImage 가
있으며 이 클래스는 인터페이스 IGLImage 클래스를 상속 받습니다 . 이 강의의 목적은 텍스처 매핑이기 때문에 이미지에서 픽셀을 읽어오는 내용을 생략했습니다 . 참고로 BMP, TGA 파일은 픽셀 순서대로 읽으면 되나 PNG 의 경우 이미지의
위 , 아래를 바꾸어 주어야 합니다 . 그런데 오히려 BMP, TGA 를 반대로 적용하고 PNG 를 그대로 적용하는 시스템도
있습니다 .



OpenGL 에서 다중 텍스처 (Multi-Texturing) 처리를 간단히 정리하면 다음과 같습니다 .



1. 다중 텍스처 지원을 확인한다 .

2. 확장 함수 포인터를 얻는다 .

3. 텍스처를 생성한다 .

4. 다중 텍스처 단계에 대한 필터링 , 어드레싱 색상 연산 (Operation) 을 지정한다 .

5. 텍스처 좌표를 지정하고 모델을 그린다 .



하드웨어에서 다중 텍스처 지원이 되는지 glGetString() 함수를
통해서 확장된 목록을 구합니다 . 그리고 이 목록에서 "GL_ARB_multitexture" 이 있는지 확인합니다 . 목록에
대한 확장 내용은 공백 (' ') 으로 구분되어 있습니다 . 여러분은
이 목록을 구분하기 위해서 다음과 같은 함수를 만들어야 합니다 .



INT EnumExtension ( char * sCheck, char * sGetStrings)

{

char *   sSrc=
sGetStrings;

char seps[]  
= " " ;

char *   token;



token
= strtok (
sSrc, seps );

while ( token != NULL )

{

if ( 0 == _stricmp (sCheck, token))

return 0 ;



token
= strtok ( NULL ,
seps );

}



return - 1 ;

}

…

char * sExtension = ( char *) glGetString ( GL_EXTENSIONS );

hr = EnumExtension ( "GL_ARB_multitexture" , sExtension);



strtok() 함수는 구분자 (Delimit) 와
문자열을 입력하면 해당 문자열을 구분자에 지정된 기호들로 분리해 주는 편리한 함수 입니다 . 게임 프로그램에서
자료를 해석할 때 자주 사용되는 함수입니다 .



확장 함수 포인터를 얻어오기 위한 함수는 위글 함수 wglGetProcAddress() 를
이용합니다 . 이 함수는 DLL 에서 함수를 얻고자 할 때 사용하는
함수 GetProcAddress() 와 많이 닮았습니다 .

다중 텍스처에 사용되는 함수는 glMultiTexCoord2fARB(), glActiveTextureARB(),
glClient-ActiveTextureARB() 입니다 . 이 함수를 사용하기 위해서 먼저
다음과 같이 함수 포인터를 작성합니다 .



PFNGLMULTITEXCOORD2FARBPROC glMultiTexCoord2fARB
= NULL ;

PFNGLACTIVETEXTUREARBPROC glActiveTextureARB
= NULL ;

PFNGLCLIENTACTIVETEXTUREARBPROC glClientActiveTextureARB = NULL ;



PFNGLMULTITEXCOORD2FARBPROC, PFNGLACTIVETEXTUREARBPROC,
PFNGLCLIENTACTIVETEXTUREARBPROC 에 대한 형식은 glext.h 파일에
선언되어 있습니다 . 만약 여러분의 컴파일러에 glext.h 파일이
없으면 http://www.opengl.org/registry/api/glext.h 에서
최신 glext.h 파일을 구할 수 있습니다 .



CMcScene::Init() 함수에서 함수 포인터를 다음과 같이
구하고 있음을 볼 수 있습니다 .



glActiveTextureARB
=

( PFNGLACTIVETEXTUREARBPROC ) wglGetProcAddress ( "glActiveTextureARB" );

glMultiTexCoord2fARB
=

( PFNGLMULTITEXCOORD2FARBPROC ) wglGetProcAddress ( "glMultiTexCoord2fARB" );

glClientActiveTextureARB =

( PFNGLCLIENTACTIVETEXTUREARBPROC ) wglGetProcAddress ( "glClientActiveTextureARB" );



텍스처 이름을 생성하고 , 픽셀 바인딩은 넘어가겠습니다 . OpenGL 은 함수 중심으로 구성되어 있고 각 함수들이 영향을 주는 범위가 있어서 호출 순서가 중요합니다 . 렌더링에서 멀티 텍스처를 적용하는 방법에도 함수의 호출 순서를 꼭 지키는 것이 중요하며 함수 호출 순서는 glActiveTexture-ARB() à glEnable() à glBindTexture() à glTexParameteri() à glTexEnvi() à … 으로 진행 됩니다 .



glActiveTextureARB() 함수는 현재 적용되는 다중 텍스처
단위 (Stage) 를 지정하는 함수 입니다 . 이 함수는 D3D 의 다중 텍스처 단계 (Stage) 를 지정하는 것과 비슷합니다 .

glTexEnvi() 함수는 각 단계에 입력된 색상 혹은 알파 값이
기존의 값들과 연산을 지정하는 함수 입니다 . 예를 들어 0 번째
단계에서 이 함수의 세 번째 인수에 GL_MODULATE 를 지정하면 glColor*() 함수로 지정된 색상과 텍스처 색상을 곱합니다 .



glTexEnvi ( GL_TEXTURE_ENV , GL_TEXTURE_ENV_MODE , GL_MODULATE );



텍스처 색상만을 적용할 때는 세 번째 인수에 GL_DECAL 을 사용합니다 .



gl06_tex2_multi.zip 의 CMcScene::Render() 에서 렌더링에서 멀티텍스처를 적용하는 함수 함수들의 호출 순서를 볼
수 있습니다 .



void CMcScene :: Render ()

…

glActiveTextureARB ( GL_TEXTURE0_ARB );

glEnable ( GL_TEXTURE_2D );

glBindTexture ( GL_TEXTURE_2D ,
m_nTexD);

glTexParameteri ( GL_TEXTURE_2D , GL_TEXTURE_MAG_FILTER , GL_LINEAR );

glTexParameteri ( GL_TEXTURE_2D , GL_TEXTURE_WRAP_S , GL_REPEAT );

glTexEnvi ( GL_TEXTURE_ENV , GL_TEXTURE_ENV_MODE , GL_MODULATE );

…

glActiveTextureARB ( GL_TEXTURE1_ARB );

glEnable ( GL_TEXTURE_2D );

glBindTexture ( GL_TEXTURE_2D ,
m_nTexL);

glTexParameteri ( GL_TEXTURE_2D , GL_TEXTURE_MAG_FILTER , GL_LINEAR );

glTexEnvi ( GL_TEXTURE_ENV , GL_TEXTURE_ENV_MODE , GL_MODULATE );

…

DrawModel (&mtWld);



glActiveTextureARB ( GL_TEXTURE0_ARB ); glBindTexture ( GL_TEXTURE_2D , 0 );

glActiveTextureARB ( GL_TEXTURE1_ARB ); glBindTexture ( GL_TEXTURE_2D , 0 );



다중 텍스처도 각 단계에 대해서 glBindTexture
(GL_TEXTURE_2D, 0); 을 호출해서 다른 프로세스에 영향을 주지 않도록 합니다 .



gl06_tex2_multi.zip 의
색상과 glTexEnvi() 함수의 연산 방법을 바꾸면 다음과 같은 화면을 볼 수 있습니다 .





< 다중 텍스처 : gl06_tex2_multi.zip .

MODULATE- 색상 (1,1,1,1), MODULATE- 색상 (0,1,1,1),
ADD- 색상 (0,1,1,1)>



11.1.7 2D Sprite

게임에서 2D 스프라이트는 UI(User Interface) 에서 많이 사용하므로 꼭 필요합니다 . D3D 에서 우리는
이것을 RHW 를 이용해서 구현해 보았습니다 . OpenGL 은
비트맵을 glRasterpos*() 함수로 화면 좌표와 1:1 대응을
만들 수 있습니다 . 그런데 glRasterpos*() 함수는 ES 에서 지원이 안될 수도 있습니다 . 따라서 3D 에서 특정한 위치에 카메라를 고정하고 모델 - 뷰 행렬과 투영 행렬을
조정해서 구현하거나 아니면 초기 상태로 설정하고 2D 를 구현 하는 것이 바람직합니다 .



모델 - 뷰 행렬과 투영 행렬은 설정을 안 하면 ( 렌더링 머신의 디폴트 상태 ) 단위행렬이고 뷰 체적 (View-volume) 의 정점은 [-1, 1] 범위에 있습니다 . UI 는 화면을 기준으로 구성되기 때문에 화면 좌표를 정규 변환 후의 좌표 값 ([-1,
1] 범위 ) 으로 변경해야 하며 이것을 공식으로 만들면 다음과 같이 됩니다 .

정규 좌표 x =  2 * 화면 위치 x / 화면 너비 - 1

정규 좌표 y = -2 * 화면 위치 y / 화면 높이 + 1



식의 검증을 위해 화면 위치를 (0,0) 과 ( 화면 너비 , 화면 높이 ) 값을
입력하면 각각 (-1, 1), (1, -1) 이 되어 식이 올바름을 알 수 있습니다 .



정규 좌표와 더불어 ST 좌표도 구해야 하는데 S, T 좌표는 입력된 이미지 영역을 이미지의 너비와 높이를 적용하면 바로 구해집니다 . 그런데 OpenGL 은 좌 하단이 (0,0) 이고 우 상단이 (1, 1) 입니다 . 따라서
좌 하단의 ST 값은 렌더링 이미지 영역의 Left 와 , Bottom 을 이용해야 하며 t 값은 "1.0- t" 로 계산이 되어야 합니다 .



좌 하단 s = ( 렌더링 이미지 영역 ).left/ 이미지 너비 ;

좌 하단 t = 1.0 - ( 렌더링 이미지 영역 ).bottom/ 이미지 높이 ;



비슷하게 우 상단은 right, top 을 이용합니다 .



우 상단 s = ( 렌더링 이미지 영역 ).right/ 이미지 너비 ;

우 상단 t = 1.0 - ( 렌더링 이미지 영역 ).top/ 이미지 높이 ;



gl07_sprite_texclass.zip 의 CGLTexture::DrawPixel() 는 2D 스프라이트를 구현하는
함수입니다 . 이 함수에서 다음의 코드는 위치와 렌더링 이미지의 영역을 조합해서 Left, Top, Right, Bottom 을 구하는 과정입니다 .



glGetFloatv ( GL_VIEWPORT , f);



PosL = 2 . 0f * vTrn.x/f[ 2 ] - 1 . 0f ; // Left

PosT = - 2 . 0f * vTrn.y/f[ 3 ] + 1 . 0f ; // Top

PosR = PosL + 2 . 0f * rcW * vScl.x/f[ 2 ]; // Right

PosB = PosT - 2 . 0f * rcH * vScl.y/f[ 3 ]; // Bottom



텍스처 좌표 S, T 는
렌더링 이미지가 주어질 때 계산 하도록 구현되어 있습니다 .



if (rc)

{

rcW= FLOAT (rc->right
- rc->left);

rcH= FLOAT (rc->bottom-
rc->top);



st0.x
= rc->left/ImgW; // 좌 하단 S

st0.y
= 1 . 0f - rc->bottom/ImgH; // 좌 하단 T

st1.x
= rc->right /ImgW; // 우 상단 S

st1.y
= 1 . 0f - rc->top/ImgH; // 우 상단 T

}



렌더링 이미지의 4 점의 위치와 s, t 를 구하고 나서 정점을 그리기 전에 변환 행렬을 저장하고 전부 초기화 ( 단위 행렬 ) 합니다 .



// 변환 행렬 저장

float mtPrj[ 16 ]={ 0 }; float mtViw[ 16 ]={ 0 };

glGetFloatv ( GL_PROJECTION_MATRIX ,
( GLfloat *)mtPrj);

glGetFloatv ( GL_MODELVIEW_MATRIX , ( GLfloat *)mtViw);



// 변환 행렬 전부 초기화

glMatrixMode ( GL_PROJECTION ); glLoadIdentity ();

glMatrixMode ( GL_MODELVIEW ); glLoadIdentity ();



깊이 테스트를 비활성화로 설정해서 이전의 픽셀을 무조건 교체하도록 합니다 .



glDisable ( GL_DEPTH_TEST );



더불어 알파 테스트 , 안개 효과 , 조명
효과 등을 비활성화 합니다 .



glDisable ( GL_ALPHA_TEST );

glDisable ( GL_LIGHTING );

glDisable ( GL_FOG );



텍스처 이미지와 정점의 색상이 혼합 되도록 알파 블렌딩을 활성화 합니다 . 이
때 블렌딩 옵션은 저장할 픽셀에 대해서는 GL_SRC_ALPHA 를 후면 버퍼에 저장된 픽셀에 대해서는 GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA 로 설정합니다 . 이 값은 D3D 의 알파 블렌딩의 디폴트 블렌딩과 동일합니다 .



glEnable ( GL_BLEND );

glBlendFunc ( GL_SRC_ALPHA , GL_ONE_MINUS_SRC_ALPHA );



텍스처를 활성화 하고 필터링과 어드레싱을 지정한 다음 4 정점을 그립니다 .



glEnable ( GL_TEXTURE_2D );

…

glBegin ( GL_TRIANGLE_FAN );

glColor4f (dcolor.r,
dcolor.g, dcolor.b, dcolor.a);

glTexCoord2f (st0.x,
st0.y); glVertex3f (PosL, PosB, 0 );

…

glEnd ();



렌더링이 끝난 후에 깊이 테스트를 활성화 하고 , 알파 블렌딩은 비활성화
하며 변환 행렬들은 glLoadMatrix*() 함수로 원래대로 되돌려 놓습니다 .



glEnable ( GL_DEPTH_TEST );

glDisable ( GL_BLEND );

glMatrixMode ( GL_PROJECTION ); glLoadMatrixf (mtPrj);

glMatrixMode ( GL_MODELVIEW ); glLoadMatrixf (mtViw);



gl07_sprite_texclass.zip 은 2D 스프라이트를 출력하는 예제입니다 . 자주 사용하는 텍스처를 클래스로
만들고 IGLTexture 인터페이스로 추상화했습니다 . CGLTexture 클래스는 IGLTexture 인터페이스를 구현하는 클래스 입니다 . 이 클래스는 " 텍스처 이름 " 을 만들고 , 2D 스프라이트를 출력하는 DrawPixel() 함수가 구현되어 있습니다 . CGLTexture 클래스의 Create() 함수는 필터링이 지정 되지 않으면 텍스처를 2D 스프라이트로
그리는 용도로 간주하고 밉맵 생성을 안 하도록 작성되어 있습니다 .

D3D 기반으로 구성된 프로그램과 호환을 위해서 IGLSprite 인터페이스도 간단하게 구현되어 있습니다 . 실행하면
다음과 같은 UI 를 볼 수 있습니다 .





<OpenGL 로 구현한 2D
Sprite: gl07_sprite_texclass.zip >





11.1.8 폰트

문자열 출력은 시스템 영역이어서 OpenGL 자체로는 문자열을 출력할
수 없고 시스템의 지원을 받아서 출력해야 합니다 . 윈도우 시스템의 경우 OpenGL 에 대해서 GDI(Graphic Device Interface) 를
이용한 방법과 위글 (wgl) 함수를 이용하는 방법 , 두 가지
출력 방식이 있습니다 .

GDI 를 이용한 방법을 먼저 보이고 , 다음으로 위글 함수를 이용한 방법을 소개하겠습니다 .



GDI 방법은 다음 그림처럼 문자열을 GDI 에 출력합니다 . 그 다음 GDI 의 메모리를 얻어와서 알파 블렌딩이 적용될 수 있도록 32 비트 픽셀로 만듭니다 . 마지막에는 OpenGL 함수로 텍스처를 생성하고 이 32 비트로 전환된 메모리를 바인딩 합니다 .





<GDI 를 이용한 출력 방법 >



문자열을 출력하려면 폰트 객체가 필요합니다 . Win API 함수 CreateFont(), CreateIndirectFont() 함수를 이용해서 폰트 객체를 생성합니다 .



m_hFnt = CreateFontIndirect (&lFont);



GDI 에 문자열을 출력해야 하는데 정확한 GDI 의 크기를 만들기 위해서 GetTextExtentPoint32() 와 GetTextMetrics() 함수를 사용해서 폰트를 적용한 문자열이 출력되는 폭을 계산합니다 .



hDC = GetDC ( NULL );

SelectObject (hDC, m_hFnt); // Parent window 의 DC 를 이용해 스트링의 정확한 픽셀 사이즈를 얻어 온다 .

GetTextExtentPoint32 (hDC, m_sMsg,
iLen, &sz);

nImgW = sz.cx;

GetTextMetrics (hDC, &tm);

nImgW -= tm.tmOverhang;





DIB(Device Interface Bitmap) 을 정의하고 CreateDIBSection() 함수로 DIB Section 을 만듭니다 . 이 때 픽셀에 대한 주소를 얻기 위해 CreateDIBSection() 함수의
인수로 전달합니다 .





BITMAPINFO BmpInfo={ 0 };

…

BYTE *   pPxlT = NULL ;

HBITMAP hBmpCur = :: CreateDIBSection (hDC, &BmpInfo, DIB_RGB_COLORS , ( void **)&pPxlT, 0 , 0 );



앞에서 정의한 HDC 를 그대로 사용하면 바탕화면에 문자열이 출력되고
또한 현재 화면의 DC 를 가져오기가 불편함으로 CreateCompatibleDC() 함수로
현재의 DC 와 동등한 DC 를 만들고 이 DC 에 DIB Section 을 연결합니다 .



HDC hMemDC = :: CreateCompatibleDC (hDC);

HBITMAP hBmpOld = ( HBITMAP ):: SelectObject (hMemDC, hBmpCur);



문자열을 DC 에 출력하면 비트맵에 저장이 됩니다 . 배경을 투명으로 처리하고 ExtTextOut() 함수로 문자열을
출력합니다 . 출력이 완료되면 임시로 사용한 MemDC 를
해제 합니다 .



SetBkMode (hMemDC, TRANSPARENT );

:: ExtTextOut (hMemDC, 0 , 0 , 0 , NULL , m_sMsg, iLen, NULL );



:: SelectObject (hMemDC,
hBmpOld); // 이전 비트맵으로 복구

::DeleteDC (hMemDC); // 임시 DC 를 제거



이렇게 GDI 로 문자열 출력이 끝났습니다 .

다음으로 이 GDI 에서 픽셀 주소에 얻어와 텍스처에 사용할 메모리에
복사를 합니다 . GDI 의 픽셀 주소는 CreateDIBSection() 함수의 인수로 사용했던 pPxlT 변수 입니다 .



다음과 같이 32 비트 픽셀을 만듭니다 .



BYTE *   pPxl= new BYTE [ 4 * nImgW * nImgH];



DIB 는 24 비트 이므로 32 비트로 복사를 할 때 Red = 0, Green = 0, Blue = 0 이면
알파를 0 으로 설정합니다 .



INT n1 = (y*nImgW + x)* 3 ; INT n2 = (y*nImgW + x)* 4 ;

BYTE R = pPxlT[n1 + 0 ]; BYTE G = pPxlT[n1 + 1 ]; BYTE B = pPxlT[n1 + 2 ];

pPxl[n2 + 0 ] = R; pPxl[n2 + 1 ] = G; pPxl[n2 + 2 ] = B;

pPxl[n2 + 3 ] = 0xFF ;



if ( 0 == R && 0 == G && 0 == B)

pPxl[n2
+ 3 ] = 0x0 ;



픽셀 데이터 , 너비 , 높이 , 32 비트에 대한 포맷 GL_RGBA 가 만들어졌으므로 우리는 OpenGL 의 텍스처를 만들고 이 픽셀을 바인딩 할 수 있게 됩니다 .



gl08_font01_gdi1.zip 은 GDI 에 문자열을 출력하고 , 픽셀을 얻어와서 텍스처를 생성하는 예제입니다 . 이전의 CGLTexture 클래스는 파일에서만 텍스처를 만들었으나
지금 같은 상황처럼 실시간으로 생성된 메모리에서도 텍스처를 만들어야 하므로 이를 처리할 수 있도록 약간의 수정을 가했습니다 . 어렵지 않은 내용이므로 설명을 생략하겠습니다 .

gl08_font01_gdi2.zip 는 gl08_font01_gdi1.zip 을
수정해서 GDI 를 이용한 폰트 클래스가 적용된 예제입니다 . 이
둘을 실행하면 다음과 같은 화면을 볼 수 있습니다 .





<GDI 를 이용한 문자열 출력 : gl08_font01_gdi1.zip , gl08_font01_gdi2.zip >



문자열 출력에 대한 위글 함수는 문자열에 대한 2 차원 비트맵을 만들고
출력하는 wglUseFontBitmapsW() 함수와 3 차원
정점 리스트를 만드는 wglUseFontOutlinesW() 함수 2 가지
있습니다 .

이들 함수들의 동작을 이해하기 위해서는 OpenGL 의 디스플레이 목록 (Display List) 를 알아야 합니다 . 디스플레이 목록은 그래픽
파이프에 대한 명령을 미리 처리하거나 또는 처리되는 과정을 저장해서 렌더링의 효율을 높이도록 하는 것입니다 .

디스플레이 목록을 만드는 방법은 먼저 glGenLists() 함수로 " 디스플레이 이름 " 을 생성합니다 .



GLuint m_nGeo;

m_nGeo = glGenLists ( 1 );



다음으로 OpenGL 에 대한 명령어들을 glNewList()/glEnd() 함수 사이에 기록하면 디스플레이 이름에 명령어들이 바인딩 하게 됩니다 . 다음은 정점 렌더링을 디스플레이 목록에 바인딩하는 예입니다 .



glNewList (m_nGeo, GL_COMPILE );

glBegin ( GL_TRIANGLE_FAN );

glColor4f ( 1 , 1 , 1 , 1 ); glVertex3f ( 0 . 0f , 0 . 0f , 0 );

glColor4f ( 1 , 0 , 0 , 1 ); glVertex3f ( 1 . 0f , 0 . 0f , 0 );

glColor4f ( 1 , 0 , 1 , 1 ); glVertex3f ( 0 . 7f , 0 . 7f , 0 );

…

glEnd ();

glEndList ();



디스플레이 목록을 만들었으면 glCallList() 또는 glCallLists() 함수를 호출하면 glNewList() / glEndList() 에
등록된 명령어들을 실행합니다 .



glCallList (m_nGeo);



디스플레이 목록은 glDeleteLists() 함수로 해제합니다 .



glDeleteLists (m_nGeo, 1 );



원칙적으로 모든 OpenGL 의 명령어는 디스플레이 목록에 적용할 수
있지만 목록에 적용되지 않고 즉시 실행하거나 아니면 무한 루프에 빠지게 되는 상황에 대해서는 OpenGL 도움말을
이용하기 바랍니다 .

앞의 코드에 대한 예는 gl08_font03_displaylist.zip 을 참고하기
바랍니다 .



폰트를 생성하고 BOOL wglUseFontBitmaps ( HDC hdc, DWORD first, DWORD count, DWORD listBase) 함수를 호출하면 이 함수는 해당 DC 에서 설정한
폰트에 대해서 first 에 해당하는 문자에서 count 만큼
비트맵을 내부적으로 만듭니다 .



예를 들어 다음과 같은 코드가 있으면



GLuint sList = glGenLists ( 96 );

HFONT hFont = CreateFont (…);

wglUseFontBitmaps (hDC, 32 , 96 , sList);



이 것은 ASCII 32 번부터 96 개의 문자에 대한 비트맵을 만들고 이것을 디스플레이 목록 sList 에
바인딩하는 것을 의미합니다 .

아스키 문자열에 대한 호출은 다음과 같이 합니다 .



glPushAttrib ( GL_LIST_BIT );

glListBase ( sList - 32 );

glCallLists ( strlen ( "ASCII 문자열 " ), GL_UNSIGNED_BYTE , sList);

glPopAttrib ();



그런데 한글은 2 바이트 문자입니다 . 따라서 VC 프로젝트가 Multi-Byte 로
설정되어 있는 경우에 wglUseFontBitmaps() 함수 대신 wglUseFontBitmapsW() 함수를
강제로 호출해야 합니다 . 그리고 한가지 더 한글을 앞의 코드처럼 적용하면 디스플레이 목록이 거의 2 16 정도 크기를 가져야 ASCII 의 예제처럼
사용할 수 있을 것입니다 . 이것은 심각한 메모리 낭비가 아닐 수 없습니다 . 한글 문자열은 한꺼번에 다 만들어 사용하지 않고 한 문자당 하나의 디스플레이 목록을 만들어서 사용합니다 .

이를 위해 먼저 다음과 같이 Wide-Char 형 문자와 디스플레이
목록을 한 쌍으로 하는 구조체와 이 구조체에 대한 벡터 컨테이너를 준비합니다 .



struct TwcharLst

{

WCHAR c;

GLuint l;

};



vector < TwcharLst >      m_vStr;



입력된 Multi-Byte 문자열을 한 문자씩 Wide-Char 형으로 변경하면서 벡터 컨테이너에 넣습니다 .



char *   s=sMsg;



for (i= 0 ; i< strlen (sMsg); ++i)

{

WCHAR t= 0 ;

if (*s> 0 )

{

MultiByteToWideChar ( CP_ACP , MB_PRECOMPOSED , s, - 1 , &t, 1 ); ++s;

}

else

{

MultiByteToWideChar ( CP_ACP , MB_PRECOMPOSED , s, - 1 , &t, 2 ); s+= 2 ;

}

m_vStr. push_back ( TwcharLst (t, 0 ));

}



앞에서 Wide-Char 형으로 변경한 각각의 문자들에 대해서 디스플레이
목록을 만들고 wglUseFontBitmapsW() 함수로 바인딩 합니다 .



for (i= 0 ; i<m_vStr. size (); ++i)

{

UINT uList = glGenLists ( 1 );

wglUseFontBitmapsW (hDC,
m_vStr[i].c, 1 , uList);

m_vStr[i].l
= uList;

}



문자열 출력은 벡터 컨테이너의 디스플레이 목록을 출력하는 것과 동일합니다 .



for ( INT i= 0 ; i<m_vStr. size (); ++i)

{

glPushMatrix ();

…

glPushAttrib ( GL_LIST_BIT );

glCallList (m_vStr[i].l);

glPopAttrib ();



glPopMatrix ();



전체 코드는 gl08_font02_wgl_2d.zip 을
참고하기 바랍니다 .





< wglUseFontBitmapsW() 함수를 이용한 비트맵
출력 . gl08_font02_wgl_2d.zip >



3 차원 문자열을 출력하는 방법은 wglUseFontOutlinesW() 함수를 사용하는 것 이외에 2 차원 비트맵 폰트로 출력하는 것과 차이가 거의 없습니다 . 먼저 이전의 구조체를 변경해서 문자의 모양이 저장될 수 있도록 글리프 (Glyph) 변수를 추가합니다 .



struct TwcharLst

{

WCHAR c;

GLuint l;

GLYPHMETRICSFLOAT GMF;

};



디스플레이 목록을 만들 때 wglUseFontOutlinesW() 함수를
사용합니다 .



for (i= 0 ; i<m_vStr. size (); ++i)

{

UINT uList = glGenLists ( 1 );

wglUseFontOutlinesW (hDC,
m_vStr[i].c, 1 , uList

, 0 . 0f , 10 . 0f

, WGL_FONT_POLYGONS , &m_vStr[i].GMF);



m_vStr[i].l
= uList;

}



렌더링에서는 글리프 값을 이용해서 문자의 간격을 조정합니다 .



for ( INT i= 0 ; i<m_vStr. size (); ++i)

{

glPushMatrix ();

…

glPushAttrib ( GL_LIST_BIT );

glCallList (m_vStr[i].l);

glPopAttrib ();



glPopMatrix ();



xPos
+= m_vStr[i].GMF.gmfCellIncX;

}



gl08_font02_wgl_3d.zip 을
실행하면 다음과 같은 3D 문자열 출력을 볼 수 있습니다 .





<3 차원 문자열 출력 : gl08_font02_wgl_3d.zip >





11.1.9 카메라

우리는 지금까지 게임 프로그램에서 사용할 OpenGL 의 내용을 살펴
보았습니다 . 이외에 OpenGL 의 내용을 상당히 많이 있지만
이 후의 기초적인 내용은 다음의 OpenGL ES 에서 다시 추가해서 설명하겠습니다 .

3D 게임 프로그램의 시작은 카메라 클래스를 만드는 일이며 카메라
클래스는 뷰 변환 행렬 (View Transform Matrix) 과 투영 변환 행렬 (Projection Transform Matrix) 을 만드는 것입니다 . 이것을
잘 구성해 놓으면 ES 에서도 구조의 변함 없이 잘 동작하는 카메라가 될 수 있습니다 .

보통 OpenGL 에서 카메라를 만들면 대부분의 사람들은 다음의 왼쪽
그림과 같이 Y 축이 위를 향하고 Z 축이 나오는 형태의 카메라를
만듭니다 .





<Z 축이 나오는 방향 , Y 축이
들어가는 방향 >



그런데 이것을 크게 생각하지 않고 계속 게임 프로그램을 만드는 도중에 회전의 방향과 위치 , 그리고 삼각형의 은면 제거 (Culling) 등을 적용할 때 혼란스러운
점이 많이 발생합니다 . 물론 이것이 큰 문제는 아닙니다 . 그런데
처음부터 오른쪽과 같은 형태의 카메라를 만들면 이런 문제들을 피할 수가 있습니다 . 무엇보다도 마음에
드는 것은 수학 공식을 사용한 알고리즘 적용이 쉽고 , 그림을 그려서 문제를 해결하려고 할 때 Y 가 들어가는 방향이면 직관적입니다 . 또한 3DS MAX 의 화면과 일치해서 플러그인 (Plugin) 만들 때도
유리합니다 . 가장 큰 매력은 D3D 에서 만든 데이터를 Y 와 Z 교환 만으로 OpenGL 시스템에
적용할 수 있다는 것입니다 . 특히 에뮬레이터나 툴 (Tool) 프로그램을 D3D 로 만들고 게임 시스템은 OpenGL 을 만들 때 오른쪽과 같은
형태의 카메라는 여러모로 이점이 많다고 할 수 있습니다 .



Y 축이 들어가는 방향의 카메라를 적용하기 위해서 gluLookAt() 함수를 사용하려면 카메라의 입력 값 Up 벡터를 (0, 0, 1) 방향으로 해야 합니다 .



glMatrixMode ( GL_MODELVIEW );

gluLookAt (…, 0 , 0 , 1 );



DXSDK 함수 D3DXMatrixLookAtRH() 을
이용할 때도 마지막 Up 벡터의 방향을 다음과 같이 (0,0,1) 로
합니다 .



glMatrixMode ( GL_MODELVIEW );

D3DXMatrixLookAtRH (&m_mtViw, …, & D3DXVECTOR3 ( 0 , 0 , 1 ));

glLoadMatrixf (( FLOAT *)&m_mtViw);



만야 카메라에 gluPerspective() 함수 대신 D3DXMatrixPerspectiveFovRH() 함수를 사용하려면 먼저 이 둘의 차이를 이해해야 합니다 .

gluPerspective() 함수는 다음과 같은 행렬을 만들고 이것을
렌더링 머신의 투영 변환에 적용하는 함수입니다 .







D3DXMatrixPerspectiveFovRH() 함수는 다음과
같은 행렬만 만들어 냅니다 .







따라서 다음과 같이 투영 행렬을 만들고 _33 과 _43 을 OpenGL 에 맞게 수정한 다음 glLoadMatrixf() 함수를 호출해서 파이프라인에 연결합니다 .



D3DXMATRIX m_mtPrj; // 투영 행렬

…

D3DXMatrixPerspectiveFovRH (&m_mtPrj

, D3DXToRadian (m_fFv), m_fAs, m_fNr, m_fFr);



m_mtPrj._33 =  (m_fNr+m_fFr)/(m_fNr -m_fFr);

m_mtPrj._43 = 2.0f * m_fNr * m_fFr/(m_fNr-m_fFr);



glMatrixMode ( GL_PROJECTION );

glLoadMatrixf (( FLOAT *)&m_mtPrj);



뷰 행렬 , 투영 행렬에 대한 핵심 코드들을 살펴보았습니다 . 이제 카메라 클래스를 만들어 봅시다 . 클래스 구성은 카메라 인터페이스를
담당하는 ILcCam 클래스를 만들어 추상화 하고 구현은 CLcCam 클래스를 두어 코드가 장차 게임 엔진에 포함 될 수 있도록 구조화합니다 .

1 인칭 카메라에서 전후방 이동에 대한 MoveForward() 함수를 , 측면 이동은 MoveSideward() 함수를 그리고 회전에 대해서는 Rotation() 함수를
인터페이스로 둡니다 .

아울러 렌더링에서 투영 변환과 뷰 변환을 렌더링 머신에 적용하는 TransformProj() 함수와 TransformView() 함수도 인터페이스 함수로
구성합니다 .



interface ILcCam

…

virtual void MoveForward ( FLOAT fSpeed, FLOAT fD= 0 )= 0 ;

virtual void MoveSideward ( FLOAT fSpeed)= 0 ;

virtual void Rotation ( FLOAT fYaw, FLOAT fPitch, FLOAT fSpeed)= 0 ;



virtual void TransformProj ()= 0 ;

virtual void TransformView ()= 0 ;

};



이제 ILcCam 클래스를 상속 받는 CLcCam 클래스에서 앞의 함수들을 구현할 차례입니다 .



먼저 전후방 이동에 대해서 구현해 봅시다 . 전후방 이동은 그림처럼
카메라의 시선 방향으로 움직이는 것을 의미합니다 .







그런데 앞서서 우리는 Y 축이 들어가는 방향으로 카메라를 구성한다고
했으므로 이전에 만들어 놓은 뷰 행렬의 뷰 행렬의 Y 축에 해당하는 _12, _22, _32 값을 시선 방향으로 만들 수 있습니다 .



시선 방향 벡터 = D3DXVECTOR3(m_mtViw._12,
m_mtViw._22, m_mtViw._32)



하지만 Y 축이 들어가는 방향으로 설정해도 Up 벡터가 (0,0,1) 로 되어 있어 시선 방향은 여전히 _13, _23, _33 값입니다 .



시선 방향 벡터 = - D3DXVECTOR3(m_mtViw._13,
m_mtViw._23, m_mtViw._33)



이것은 종종 혼동하는 일들 중에 하나 입니다 . 이런 것을 겪지 않으려면
원칙에 충실하는 것이 가장 좋습니다 . 시선 방향이라는 것은 Look 벡터에서
카메라의 Eye 벡터를 빼고 이를 정규화한 벡터로 하면 문제는 없어 집니다 .



시선 방향 벡터 = Normalize(Look 벡터 - Eye 벡터 )



그리고 카메라의 전후방 이동은 행렬은 앞의 그림처럼 시선 방향 벡터에 적당한 이동 거리를 곱하고 이를 Look 벡터와 Eye 벡터에 더하고 나서 뷰 행렬을 만들면 됩니다 .



Look 벡터 += 시선 방향 벡터 * Distance

Eye 벡터 += 시선 방향 벡터 * Distance



gl09_camera02.zip 의 CLcCam::MoveForward() 함수는 지금까지 설명한 방법을 적용해서 뷰 행렬을 구하는 함수
입니다 .



void CLcCam :: MoveForward ( FLOAT fSpeed , FLOAT fD )

{

D3DXVECTOR3 vcZ ;



vcZ = m_vcLook - m_vcEye ;

D3DXVec3Normalize (& vcZ , & vcZ );



m_vcEye += vcZ * fSpeed ;

m_vcLook += vcZ * fSpeed ;



D3DXMatrixLookAtRH (& m_mtViw , & m_vcEye , & m_vcLook ,
& m_vcUp );

}



측면 이동은 시선 방향 벡터와 Up 벡터에 수직 방향으로 움직이는
것입니다 . 따라서 앞에서처럼 시선 방향 벡터를 구하고 외적을 이용해서 시선 방향 벡터와 Up 벡터의 수직 벡터를 구합니다 . 이 수직 벡터를 크기가 1 인 단위벡터로 정규화하고 이동 거리를 곱한 다음 Look 벡터 , Eye 벡터에 더하면 측면 이동을 구현하게 되는 것입니다 .





< 측면 이동 >



시선 방향 벡터 = Normalize(Look 벡터 - Eye 벡터 )

측면 방향 벡터 = Normalize( Cross( 시선 방향
벡터 , Up 벡터 ) )

Look 벡터 += 측면 방향 벡터 * Distance

Eye 벡터 += 측면 방향 벡터 * Distance



이것은 gl09_camera02.zip 의 CLcCam::MoveSideward() 함수에서 다음과 같이 구현되어 있습니다 .



void CLcCam :: MoveSideward ( FLOAT fSpeed)

{

D3DXVECTOR3 vcZ =
m_vcLook - m_vcEye;

D3DXVec3Normalize (&vcZ,
&vcZ);



D3DXVECTOR3 vcX;

D3DXVec3Cross (&vcX,
&vcZ, &m_vcUp);

D3DXVec3Normalize (&vcX,
&vcX);

m_vcEye 
+= vcX * fSpeed;

m_vcLook
+= vcX * fSpeed;



D3DXMatrixLookAtRH (&m_mtViw,
&m_vcEye, &m_vcLook, &m_vcUp);

}



1 인칭 카메라의 회전은 그림처럼 회전에 대해서 Look 벡터 - Eye 벡터를 각도 θ만큼 회전 시키고 이것을 Eye 벡터에 더해서 새로운 Look 벡터를 만드는 것입니다 .





이것을 구현하려면 Yaw 와 Pitch 를 각각 적용하는 것이 수월합니다 .



Yaw 는 현재 Z 축에 해당하므로 Yaw 값으로 Z 축에 대한 회전 행렬을 만듭니다 . 새로운 Look 벡터는 다음과 같이 구합니다 .



시선 벡터 = Look 벡터 - Eye 벡터

회전 행렬 = RotationMatrixZ(Yaw)

시선 벡터 ' = 시선 벡터 * 회전 행렬

Up 벡터 ' =
Up 벡터 * 회전 행렬

Look 벡터 '
= Eye 벡터 + 시선 벡터 '



Pitch 는 Yaw 와 처리하는
절차는 같지만 회전 행렬은 카메라의 X 축에 대한 회전이므로 뷰 행렬의 _11, _21, _31 을 회전축으로 하고 Pitch 만큼 회전한 행렬을 구해서 적용합니다 .



시선 벡터 = Look 벡터 - Eye 벡터

회전 행렬 = RotationMatrixAxis(

Axis(ViewMatrix._11,
ViewMatrix._21, ViewMatrix._31)

,
Pitch)

시선 벡터 ' = 시선 벡터 * 회전 행렬

Up 벡터 ' =
Up 벡터 * 회전 행렬

Look 벡터 '
= Eye 벡터 + 시선 벡터 '



gl09_camera02.zip 의 CLcCam::Rotation() 함수는 지금까지 설명한 방법을 적용해서 뷰 행렬을 구하는 함수 입니다 .



void CLcCam :: Rotation ( FLOAT fYaw, FLOAT fPitch, FLOAT fSpeed)

{

m_fYaw  
= D3DXToRadian (fYaw  * fSpeed);

m_fPitch
= D3DXToRadian (fPitch* fSpeed);



D3DXMATRIX rot;

D3DXVECTOR3 vcZ;

D3DXVECTOR3 vcX;



// Yaw 에 대한 회전

vcZ
= m_vcLook - m_vcEye;

D3DXMatrixRotationZ (&rot,
m_fYaw);



D3DXVec3TransformCoord (&vcZ,
&vcZ, &rot);

D3DXVec3TransformCoord (&m_vcUp,
&m_vcUp, &rot);



m_vcLook
= vcZ + m_vcEye;

D3DXMatrixLookAtRH (&m_mtViw,
&m_vcEye, &m_vcLook, &m_vcUp);



// Pitch 에 대한 회전

vcZ
= m_vcLook - m_vcEye;

vcX
= D3DXVECTOR3 (m_mtViw._11,
m_mtViw._21, m_mtViw._31);



D3DXMatrixRotationAxis (&rot,
&vcX, m_fPitch);

D3DXVec3TransformCoord (&vcZ,
&vcZ, &rot);

D3DXVec3TransformCoord (&m_vcUp,
&m_vcUp, &rot);



m_vcLook
= vcZ + m_vcEye;

D3DXMatrixLookAtRH (&m_mtViw,
&m_vcEye, &m_vcLook, &m_vcUp);

}



카메라에서 가장 중요한 이동과 회전을 구현했습니다 . 나머지 TramsformProj() 함수와 TransformView() 함수로
뷰 행렬과 투영 행렬을 그래픽 파이프라인에 연결합니다 .



void CLcCam :: TransformProj ()

{

glViewport ( 0 , 0 , INT (m_nScnW), INT (m_nScnH) );

glMatrixMode ( GL_PROJECTION );

glLoadMatrixf (( FLOAT *)&m_mtPrj);

}



void CLcCam :: TransformView ()

{

glMatrixMode ( GL_MODELVIEW );

glLoadMatrixf (( FLOAT *)&m_mtViw);

}



gl09_camera02.zip 을
실행하고 'W', 'S', 'A', 'D', 'UP', 'DOWN', 'LEFT', 'RIGHT' 을
누르면 이동과 회전을 볼 수 있습니다 .





<OpenGL 1 인칭 카메라 : gl09_camera02.zip >





11.1.10 정점 배열과 ASE

11.1.10.1 정점 배열 (Vertex Array) 사용

D3D 는 정점 버퍼를 만들어서 장면을 만듭니다 . OpenGL 도 정점을 개별적으로 처리하는 glVetex*() 함수
이외에 연속된 정점 배열 ( 버퍼 ) 를 처리하는 방법을 제공합니다 . 정점 배열 ( 버퍼 ) 를 사용하려면 void glEnableClientState ( GLenum array) 함수를 호출해서
활성화해야 합니다 .



자주 사용되는 array 인수의 종류는 다음과 같습니다 .

GL_VERTEX_ARRAY: 정점의 위치 벡터를 저장한 배열을 활성화 .

GL_NORMAL_ARRAY: 정점의 법선 벡터를 저장한 배열을 활성화

GL_COLOR_ARRAY: 정점의 색상 (Diffuse) 을 저장한 배열을 활성화 .

GL_TEXTURE_ARRAY: 정점의 텍스처 좌표를 저장한 배열을
활성화 .



만약 정점 배열의 각 성분 또는 서로 다른 배열을 조합해서 파이프라인에 위치 + 법선 벡터 + 색상 (Diffuse) + 텍스처
좌표를 적용한다고 했을 때 이들 배열을 사용하기 위해서 다음과 같이 활성화 합니다 .



glEnableClientState ( GL_VERTEX_ARRAY );

glEnableClientState ( GL_NORMAL_ARRAY );

glEnableClientState ( GL_COLOR_ARRAY );

glEnableClientState ( GL_TEXTURE_COORD_ARRAY );



사용이 끝나면 glDisableClientState() 함수를 호출해서
다른 프로세스에 영향을 주지 않도록 합니다 .



glDisableClientState ( GL_VERTEX_ARRAY );

glDisableClientState ( GL_NORMAL_ARRAY );

glDisableClientState ( GL_COLOR_ARRAY );

glDisableClientState ( GL_TEXTURE_COORD_ARRAY );



D3D 는 SetStreamSource() 함수를 이용해서 정점 스트림 ( 버퍼 ) 을 파이프라인에 연결했으며 이 때 스트림은 각각 분리된 형태로도 입력이 된다고 했습니다 . OpenGL 도 정점의 배열이 하나로 합쳐진 형태 또는 분리된 형태 상관 없이 차원 , 성분의 형식 , 하나의 배열 원소 크기 , 시작 주소를 정해서 배열을 사용합니다 .



정점의 위치 , 법선 벡터 , 색상 , 텍스처 좌표로 구성된 4 개의 배열을 파이프라인에 연결하려면 다음과
같이 작성합니다 .



FLOAT Position_Array[] ;

FLOAT Normal_Array[] ;

FLOAT Diffuse_Array[] ;

FLOAT TexCoord_Array[] ;

…

glVertexPointer ( 3 , GL_FLOAT , 0 ,  Position_Array);

glNormalPointer ( GL_FLOAT , 0 , Normal_Array );

glColorPointer ( 4 , GL_FLOAT , 0 , Diffuse_Array );

glTexCoordPointer ( 2 , GL_FLOAT , 0 , TexCoord_Array );



gl*Pointer() 함수의 인수 첫 번째는 각 배열의 원소를 구성하는
성분의 수 입니다 . 두 번째 인수는 이 성분의 데이터 형입니다 . 세
번째 인수는 연속된 배열 원소들의 Byte Offset 을 지정합니다 . 네 번째 인수는 시작 주소를 지정합니다 .



여러분은 앞에서처럼 각각 분리된 형태보다 하나로 합쳐진 형태를 많이 사용할 것입니다 . 만약 하나의 정점에 위치 + 법선 + 색상 + 텍스처 좌표로 구성된 정점 구조체를 사용하고 이것을 배열을 만들었을 때를 살펴
봅시다 .



struct VtxNDUV

{

FLOAT p[ 3 ]; // 위치

FLOAT n[ 3 ]; // 법선 벡터

FLOAT d[ 4 ]; // 색상 (Diffuse)

FLOAT s,t; // 텍스처 좌표

};



이 구조체에 대한 배열을 new 연산자로 만들었을 때 gl*Pointer() 함수 사용에 세 번째 인수의 Byte Offset 값을
정점 구조체의 크기를 전달하고 네 번째 인수는 다음과 같이 정점을 char 형으로 캐스팅해서 사용하면
편리합니다 .



INT nVtxSize = 100 ;

VtxNDUV * pVtx = new VtxNDUV [nVtxSize];

…

char * p = ( char *)pVtx;

glVertexPointer ( 3 , GL_FLOAT , sizeof ( VtxNDUV ), p); p += sizeof ( FLOAT) * 3 ;

glNormalPointer ( GL_FLOAT , sizeof ( VtxNDUV ), p); p += sizeof ( FLOAT) * 3 ;

glColorPointer ( 4 , GL_FLOAT , sizeof ( VtxNDUV ), p); p += sizeof ( FLOAT) * 4 ;

glTexCoordPointer ( 2 , GL_FLOAT , sizeof ( VtxNDUV ), p);



이렇게 정점 배열을 파이프라인에 연결한 후에 마지막으로 정점 데이터에 대한 인덱스가 없을 때는 glDrawArrays() 함수를 , 인덱스가 있으면 glDrawElements() 함수를 호출해서 렌더링을 명령합니다 .



glDrawArrays ( GL_"MODE" , " 시작 인덱스 " , " 정점 개수 " );

…

glDrawElements ( GL_"MODE" , " 인덱스 수 " , GL_" 인덱스
타입 " , " 인덱스 시작 주소 ");



glDrawElements() 함수의 인수 중에 인덱스 수 계산은



인덱스 수 = 삼각형 수 *
3



로 계산 하고 인덱스 타입은 UNSIGNED_SHORT 를 가장 많이
사용합니다 .



gl10_01vertex_array.zip 의 CMcScene::Render() 를 보면 전체적인 정점 배열을 간단하게 사용하는 방법을 볼 수 있습니다 .





11.1.10.2 ASE

Z 가 위를 가리키는 카메라를 만들고 정점 배열을 알고 있으면 ASE 를 적용하는 방법은 어려움이 없습니다 . D3D 기반에서 ASE 데이터 해석을 할 때 우리는 행렬 원소의 , 좌표의 위치 , 삼각형의 인덱스 위치 등을 D3D 에 맞게 수정했습니다 . 어떤 부분이 D3D 에 맞게 고쳐야 할 지 처음 하는 분들에게는 많이 어려웠을 것입니다 . 그런데 OpenGL 을 사용하면 이러한 문제는 없어집니다 . 맥스 , OpenGL 모두 오른손 좌표계를 사용하고 카메라를 일치 시켰다면 데이터의 교환 없이 ASE 파일을 그대로 읽어 오면 됩니다 .

주의할 것은 만약 여러분이 DXSDK 에서 제공하는 수학 함수를 사용할
때 애니메이션 데이터 다루는 부분을 조심해야 하는데 ASE Parsing 에서 사원수 누적은 q3   = q1 * q2; 이어야 합니다 . 만약
여러분께서 오른손 좌표에 맞는 사원수를 만들고 회전을 누적하기 위해 사원수 곱셈을 했다면 q3 = q2 * q1 이
되어야 할 것입니다 . 그리고 애니메이션의 회전 행렬을 구할 때도 주의해야 합니다 . D3DXMatrixRotationQuaternion() 함수를 사용해서 사원수에서 행렬을 만들었으면 이 행렬을
전치 (Transpose) 해야 애니메이션이 제대로 동작을 합니다 .

이 두 가지 점만 주의하면 OpenGL 의 Character Animation 에서도 D3DX SDK 수학함수를
활용할 수 있습니다 .



D3D 에서 사용한 ASE 를
거의 그대로 사용하려면 먼저 디바이스의 DrawIndexedPrimitiveUP() 함수를 대신할 OpenGL 함수를 구성하는 것이 좋습니다 . 다음은 OpenGL 의 glEnableClientState(), gl*Pointer(),
glDrawElements() 를 사용해서 D3D 의 DrawIndexedPrimitiveUP() 일부분을 구현한 함수 입니다 .



void LcGL_DrawIndexedPrimitiveUP ( INT mode

, INT nFace // 삼각형 개수

, const void *indices // 인덱스 버퍼

, DWORD dFVF // FVF

, const void * pVtx // 정점 버퍼

, INT dStride // 한 정점의 크기

)

…

if ( D3DFVF_XYZ & dFVF)

glEnableClientState ( GL_VERTEX_ARRAY );

if ( D3DFVF_DIFFUSE & dFVF)

glEnableClientState ( GL_COLOR_ARRAY );

if ( D3DFVF_TEX1 & dFVF)

glEnableClientState ( GL_TEXTURE_COORD_ARRAY );



char * p = ( char *)pVtx;

if ( D3DFVF_XYZ & dFVF)

glVertexPointer ( 3 , GL_FLOAT , dStride, p);



if ( ( D3DFVF_XYZ | D3DFVF_DIFFUSE ) == dFVF)

{

p
+= sizeof ( D3DXVECTOR3 );

glColorPointer ( 4 , GL_FLOAT , dStride, p);

}

else if ( ( D3DFVF_XYZ | D3DFVF_TEX1 ) == dFVF)

{

p
+= sizeof ( D3DXVECTOR3 );

glTexCoordPointer ( 2 , GL_FLOAT , dStride, p);

}

else if ( ( D3DFVF_XYZ | D3DFVF_DIFFUSE | D3DFVF_TEX1 ) == dFVF)

{

p
+= sizeof ( D3DXVECTOR3 );

glColorPointer ( 4 , GL_FLOAT , dStride, p);

p
+= sizeof ( D3DXCOLOR );

glTexCoordPointer ( 2 , GL_FLOAT , dStride, p);

}



glDrawElements (mode, nFace
* 3 , GL_UNSIGNED_SHORT , indices);



ASE 파일을 Parsing 해서
렌더링을 구현했다 하더라도 우리는 여기서 한 발짝 더 나아가 Parsing 한 데이터를 구조화해야 합니다 . 제일 먼저 처리해야 할 일은 데이터를 Binary 파일로 저장하는
것입니다 . 다음으로 X-file 의 예처럼 자원을 절약하기
위해 원본은 렌더링의 참조로 사용하고 월드 행렬과 애니메이션 데이터가 포함된 ASE Instance(Clone) 을
만드는 것입니다 .





<ASE 를 활용하기 위한 클래스 다이어그램 >



그림의 CLcAse 는 ASE 파일을
해석하고 Binary 파일을 저장하는 클래스 입니다 . CLcAseInst 는
화면의 렌더링을 담당하며 월드 행렬과 애니메이션을 갱신하고 Geometry 와 Material 은 CLcAse 의 객체를 참조하는 클래스 입니다 . CLcAseB 는 Binary 파일을 해석하는 클래스입니다 . CLcAseBInst 는 CLcAseInst 와 동일한 기능을 하며
렌더링에서 CLcAseBInst 객체를 참조하는 클래스입니다 . ASE 에
대한 뷰어에서는 위의 4 개가 전부 필요할 수도 있지만 게임에서는 CLcAseB 와 CLcAseBInst 클래스 2 개만
필요합니다 .



객체 속성을 설정하거나 가져오기 위한 SetAttrib/GetAttrib() 함수가
있습니다 . 예를 들어 CLcAseBInst::SetAttrib() 함수는
다음과 같이 문자열을 해석해서 값을 설정하고 있는데 이런 방법은 속성을 설정하는 것이 증가하면 자연히 if~else 구문이 증가하지만 모든 속성에 대해서 Set/Get 함수를 만드는 것이 줄어들고
클래스를 구조화 하기에도 좋습니다 . 현재는 비교를 위해 문자열을 사용했지만 상수 (enum) 값을 가지고 만들면 좀 더 속도에 이득이 있습니다 .



INT CLcAseBInst :: SetAttrib ( char * sCmd, void * pData)

{

if ( 0 == _stricmp ( "Advance Time" , sCmd))

{

float fElapsedTime
= *(( float *)pData);

m_dTimeCur
+= fElapsedTime;

return 0 ;

}

else if ( 0 == _stricmp ( "World Matrix" , sCmd))

{

D3DXMATRIX * pTM = ( D3DXMATRIX *)pData;

m_mtWld =
*pTM;

return 0 ;

}

…

return - 1 ;

}



전체 코드는 이전의 ASE 의 확장이며 gl10_02ase.zip 을 실행하면 다음 그림과 같이 4 개의 캐릭터가 춤추고 있는 것을 볼 수 있습니다 .



<OpenGL 에서의 ASE: gl10_02ase.zip >





지금까지 게임에서 주로 사용되는 OpenGL 내용을 살펴 보았습니다 . 다음은 OpenGL 사용할 때 꼭 기억해야 할 내용입니다 .



1. OpenGL 은 오른손 좌표계입니다 .

2. 행렬은 열 중심으로 처리되며 이로 인해 DX SDK 함수를 사용할 수 있습니다 .

3. 월드 변환은 따로 존재하지 않고 모델 - 뷰 행렬 이 존재합니다 . 월드 변환을 별도로 구현 하려면 행렬
스택을 이용합니다 .

4. OpenGL 의 각도는 Degree 입니다 .

5. 디스플레이 목록을 만들면 좀 떠 빠르게 처리됩니다 .

6. 문자열 출력은 따로 제공하지 않으며 Window System 의 경우 wgl 함수를 이용합니다 . 이 때 문자열은 반드시 Wide-char 로 변환해야만 한글을 출력할
수 있습니다 .

7. 뷰 행렬은 D3DXMatrixLookAtRH() 함수로 만들 수 있습니다 . Z 축이 위를 향하는 카메라를
만들고자 한다면 Up 벡터는 (0,0,1) 입니다 .

8. OpenGL 은 투영 변환을 거치면 모든 뷰 체적 안의 정점은
전부 [-1, 1] 범위 입니다 . 따라서 D3D SDK D3DXMatrixPerspectiveFovRH() 함수로 투영 행렬을 만들고자 한다면 이 함수를
사용하고 나서 _33, _43 값을 다시 계산합니다 .

9. D3D 처럼 정점 버퍼를 glDrawArrays(),
glDrawElements() 함수를 사용해서 그리는 방법이 존재합니다 .

10. 애니메이션에서 사원수 누적은 qn * … * q2 * q1 이며 D3D SDK 를 사용할 때는 q1 * q2 * qn 입니다 .

12. D3D SDK 함수 D3DXMatrixRotationQuaternion() 를 사용해서 사원수에서 행렬을 만들면 이 행렬을 전치 (Transpose) 해야 합니다 .