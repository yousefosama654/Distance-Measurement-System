/*
 ============================================================================
 *  lcd.c
 *  Date: OCT 1, 2022
 *  Author: Yousef_Osama
 *  Description: The Source File of ICU Driver
 ============================================================================
 */
#include "icu.h"
#include "common_macros.h" /* To use the macros like SET_BIT */
#include <avr/io.h> /* To use ICU/Timer1 Registers */
#include <avr/interrupt.h> /* For ICU ISR */
#include "gpio.h"
static volatile void (*g_callback_ptr)() = NULL_PTR;

/*
 * Description: Function to set the Call Back function address.
 */
void Icu_setCallBack(void (*a_ptr)(void)) {
	g_callback_ptr = a_ptr;
}
void Icu_setEdgeDetectionType(const ICU_Edgetpe edgeType) {
	TCCR1B = (TCCR1B & 0xbf) | (edgeType << 6);
}
/*
 * Description : Function to initialize the ICU driver
 * 	1. Set the required clock.
 * 	2. Set the required edge detection.
 * 	3. Enable the Input Capture Interrupt.
 * 	4. Initialize Timer1 Registers
 */
void Icu_init(const ICU_Config *Config_Ptr) {
	GPIO_setupPinDirection(PORTD_ID, PIN6_ID, PIN_INPUT);
	SET_BIT(TCCR1A, FOC1A);
	SET_BIT(TCCR1A, FOC1B);
	TCNT1 = 0;
	ICR1 = 0;
	Icu_setEdgeDetectionType(Config_Ptr->edge);
	TCCR1B = (TCCR1B & 0xf8) | (Config_Ptr->clock);
	SET_BIT(TIMSK, TICIE1);
}
/*
 * Description: Function to disable the Timer1 to stop the ICU Driver
 */
void Icu_DeInit(void) {
	TCCR1A = 0;
	TCCR1B = 0;
	TCNT1 = 0;
	ICR1 = 0;
	CLEAR_BIT(TIMSK, TICIE1);
}
/*
 * Description: Function to get the Timer1 Value when the input is captured
 *              The value stored at Input Capture Register ICR1
 */
uint16 Icu_getInputCaptureValue(void) {
	return ICR1;
}
/*
 * Description: Function to clear the Timer1 Value to start count from ZERO
 */
void Icu_clearTimerValue(void) {
	TCNT1 = 0;
}

ISR(TIMER1_CAPT_vect) {
	if (g_callback_ptr != NULL_PTR)
		(*g_callback_ptr)();
}
