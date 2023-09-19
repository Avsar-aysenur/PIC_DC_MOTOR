# PIC_DC_MOTOR
//DC_MOTOR_DRİVER_WİTH_RAMP
#include <rampa_dc.h>
#FUSES HS


#define Q1_H         PIN_C1
#define Q3_H         PIN_C2
#define Q2_L         PIN_D1
#define Q4_L         PIN_D2

#define LED_FORWARD  PIN_B6
#define LED_BACKWARD PIN_B7

#define ON_OFF       PIN_B1

#define BTN_FORWARD  PIN_B4
#define BTN_BACKWARD PIN_B5

#define POT          PIN_A0

#define LED1         PIN_D3
#define LED2         PIN_D4
#define LED3         PIN_D5
#define LED4         PIN_D6
#define LED5         PIN_D7

#define INCREASE     PIN_E0
#define DECREASE     PIN_E1

int16 analogBilgi;
int16 i = 0;

int counter = 0;
int ButtonState;

void stop(){

   output_low (Q2_L);
   output_low (Q4_L);
   set_pwm1_duty((int16) 0);
   set_pwm2_duty((int16) 0);          
}

void FORWARD(){

   if(i < analogBilgi){ // PWM değeri pot olana kadar artır
      i++; 
      delay_ms(10); } // Bekleme süresini artır
      if(i >= analogBilgi){
      i=analogBilgi;
      }
      set_pwm1_duty((int16)i);
      output_high(Q4_L);
      output_low (Q2_L);
      set_pwm2_duty((int16)0);
      output_high(LED_FORWARD);
      output_low(LED_BACKWARD);
}

void BACKWARD(){

   if(i < analogBilgi){ // PWM değeri pot olana kadar artır
      i++; 
      delay_ms(10);} // Bekleme süresini artır 
      if( i >= analogBilgi){
      i=analogBilgi;
      }
      set_pwm2_duty((int16) i);
      output_high(Q2_L);
      output_low (Q4_L);
      set_pwm1_duty((int16) 0); 
      output_high(LED_BACKWARD);
      output_low(LED_FORWARD);
   }



void main()
{
   setup_adc(ADC_CLOCK_DIV_32);            // Denetleyici frekansının 1/32’si kullanılacak
   setup_timer_2(T2_DIV_BY_4,1023,1);       
    
   setup_ccp1(CCP_PWM);
   setup_ccp2(CCP_PWM);
   setup_adc_ports(AN0);
   
   set_tris_b(0b00110110);
   set_tris_c(0x00);
   set_tris_d(0x00);

   output_c(0x00);
   output_d(0x00);
   
   ButtonState = input(ON_OFF);
  
   
  
   
   while(TRUE)
   {
    
      set_adc_channel(0);       // AN0 numaralı kanaldan okuma yapacağımızı belirttik
      analogBilgi = read_adc(); // AN0 numaralı kanaldan analog değeri oku
      delay_ms(1);
      
      ButtonState = input(ON_OFF);     
          
   if(ButtonState == 1){
   counter++;
   while(ButtonState == input(ON_OFF) )
   delay_ms(100);}
   
   
   if(counter>=2){
   counter=0;
   }
     

   
   if(counter==1){

     if(input(BTN_FORWARD)==0 && input(BTN_BACKWARD)==0){i=0;}
        
         if(input(BTN_FORWARD)==1 && input(BTN_BACKWARD)==0){
         while(input(BTN_BACKWARD)==1 && input(BTN_FORWARD)==0){
         
         if(i < analogBilgi){ // PWM değeri pot olana kadar artır
      i++; 
      delay_ms(10); } // Bekleme süresini artır
      if(i >= analogBilgi){
      i=analogBilgi;
      }
         
         }
         FORWARD(); 
      
         }
  
       else if (input(BTN_BACKWARD)==1 && input(BTN_FORWARD)==0){    
        while(input(BTN_FORWARD)==1 && input(BTN_BACKWARD)==0){
        
            if(i < analogBilgi){ // PWM değeri pot olana kadar artır
      i++; 
      delay_ms(10); } // Bekleme süresini artır
      if(i >= analogBilgi){
      i=analogBilgi;
      }
        }
      
         BACKWARD();  
          
       }}
         
          if(counter==0){
   
   stop();
   }
         
      }
   }
