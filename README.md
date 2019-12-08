# Scheduling

/* -------------------------------------------------------------------------
	Global variables
 ------------------------------------------------------------------------- */
int color;// 전역변수 선언

/* -------------------------------------------------------------------------
	Prototypes
 ------------------------------------------------------------------------- */
//static void rootfunc(void * arg);

task_pt task_handle01;
task_pt task_handle02;

static void taskfunc01(void * arg); //task01 선언
static void taskfunc02(void * arg);//task02 선언

/* -------------------------------------------------------------------------
	Function Definitions
 ------------------------------------------------------------------------- */
int usrmain(int argc, char * argv[]) {
	int r;

	printf("\n\n\n\r");
	printf("===========================================================================\n\r");
	printf("exe_led_switch_test (build time: %s %s)\n\r", __TIME__, __DATE__);
	printf("===========================================================================\n\r");

	r = task_create(&task_handle01, taskfunc01, NULL, IDLE_PRIORITY+2, 256, "task_01");//task01함수 생성
	if (0 != r) {
		logme("fail at task_create\r\n");
	}

	r = task_create(&task_handle02, taskfunc02, NULL, IDLE_PRIORITY+1, 256, "task_02");//task02함수 생성
	if (0 != r) {
		logme("fail at task_create\r\n");
	}

	ubik_comp_start();

	return 0;
}

static void taskfunc01(void * arg) {
	ev3_sensor_init(0, COL_COLOR);//센서 초기화

	for(;;){
		switch(ev3_sensor_get(0)){
			case RED:
				color=1;
				break;
			case GREEN:
				color=2;
				break;
			case BLUE:
				color=3;
				break;
			default:
				color=4;
				break;
		}
	task_sleep(100);//task02로 CPU를 넘김
	}
}

static void taskfunc02(void * arg) {
	motor_init();//모터 초기화
	encoder_init();//인코더 초기화

	for(;;){
		switch(color){
			case 1:
				motor_set(0,200);
				break;
			case 2:
				motor_set(0,400);
				break;
			case 3:
				motor_set(0,600);
				break;
			case 4:
				motor_set(0,800);
				break;
		}
	task_sleep(100);//task01로 CPU를 넘김
	}
}