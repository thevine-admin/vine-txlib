# vine-txlib

VINE SDK v1.0 라이브러리 적용방법
1. 헤더파일과 바이너리 파일을 컴파일 환경에 추가
vine_lib.h, vine_lib.a

2. 소스코드에서 헤더파일 인클루드
#include “vine_lib.h”

3. 라이브러리 제공기능 (v1.0)

AGC (Auto Gain Control) [8K, 16K]: 통화중 송신음 음량 최적화

AGC
AGC 는 초기화, 프로세싱 함수(8K, 16K)로 구성되어 있으며 별도의 종료함수가 없습니다.
프로세싱 함수는 매 프레임마다 호출되어야 하며, 압축이 해제된 PCM raw data를 프레임(20ms) 단위로 입력받습니다.

1. 오디오시스템의 PCM Interface 초기화 부분에서 AGC 초기화 함수 호출
시스템의 초기화 함수 호출부에서 호출하여 주십시오.


    vine_result result;
    
    result = vine_agc_init(acc, actval, rxmaxvol_dbm, txlel_ctl, maxlvl, limit_onoff);
    
    if(result == vine_fail) {
    	PRINT_LOG("[VINE] Initialization failed");
    	return FALSE;
    } else {
    	PRINT_LOG("[VINE] Initialization success");
    }
    
    

2. 오디오시스템 콜백함수에서 AGC 프로세싱 함수 호출 예제

  boolean audiosys_postprocess(uint16* txin, uint16* rxin, uint16* rxout)
  {
	  VCTuningInfo vcresult;

	  vcresult = vine_agc_processnb(txin, rxin, rxout, rxcurvol_dbm, cur_mode, ctrl_clarity);
	  if(vcresult == vine_fail) {
  		return FALSE;
  	}
  	// Print tuning Message for AGC
  	PRINT_LOG("[VINE] CurNoise:%d AVGCurNoise: %d CtlSideTone: %d”, vcresult.cur_noise, vcresult.avg_noise, vcresult.ctl_sidetone);

  	return TRUE;    
  }
