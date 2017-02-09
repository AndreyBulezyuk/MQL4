#property copyright "Andrey Bulezyuk"
#property link      "https://www.in-trading.eu"
#property version   "1.00"
#property strict

input double   LotGroesse=0.1;
input int      FilterMA=200;
input int      SchnellEMA=24;
input int      LangsamEMA=52;
input int      SignalEMA=12;
input bool     StopLossVerwenden=true;
input int      StopLoss=400; //100=10Pips; 1000=100Pips
input bool     TakeProfitVerwenden=true;
input int      TakeProfit=1500; //100=10Pips; 1000=100Pips
input bool     StopLossNachziehen=true;
input int      StopLossSchritt=400;

int OnInit()
  {

   return(INIT_SUCCEEDED);
  }
  
void OnDeinit(const int reason)
  {

  }
  
void OnTick()
  {
   double FilterMAWert0 = iMA(Symbol(),Period(),FilterMA,0,2,0,0);
   double FilterMAWert1 = iMA(Symbol(),Period(),FilterMA,0,2,0,1);

   double SLTemp = 0.0;
   double TPTemp = 0.0;
   double NeuSL=0.0;

   double MACD_Hauptlinie0=iMACD(Symbol(),Period(),SchnellEMA,LangsamEMA,SignalEMA,0,MODE_MAIN,0);
   double MACD_Signallinie0=iMACD(Symbol(),Period(),SchnellEMA,LangsamEMA,SignalEMA,0,MODE_SIGNAL,0);

   double MACD_Hauptlinie1=iMACD(Symbol(),Period(),SchnellEMA,LangsamEMA,SignalEMA,0,MODE_MAIN,1);
   double MACD_Signallinie1=iMACD(Symbol(),Period(),SchnellEMA,LangsamEMA,SignalEMA,0,MODE_SIGNAL,1);

   if(OrdersTotal()<=0)
     {
      if(FilterMAWert0>FilterMAWert1) //Filter GD steigend
        {
         if(MACD_Hauptlinie1<MACD_Signallinie1 && MACD_Hauptlinie0>MACD_Signallinie0) //MACD Signal bullisch
           {
            if(AccountFreeMarginCheck(Symbol(),0,LotGroesse)>1) //Ausreichend Margin?
              {
               if(StopLossVerwenden==true)
                 {
                  SLTemp=Bid-StopLoss*Point;
                    }else{
                  SLTemp=0.0;
                 }

               if(TakeProfitVerwenden==true)
                 {
                  TPTemp=Bid+TakeProfit*Point;
                    }else{
                  TPTemp=0.0;
                 }

               if(OrderSend(Symbol(),OP_BUY,LotGroesse,Ask,0,SLTemp,TPTemp,"MACD Filtered Strategie",54321,0,clrNONE)==true)
                 {
                  Alert("Long Position (Lot:"+LotGroesse+"|SL:"+SLTemp+"|TP:"+TPTemp+") eröffnet!");
                 }
                 }else{
               Alert("Keine freie Margin nach Positionseröffnung!");
              }
           }
           }else{ //Filter bearisch
         if(MACD_Hauptlinie1>MACD_Signallinie1 && MACD_Hauptlinie0<MACD_Signallinie0)//MACD Signal bearisch
           {
            if(AccountFreeMarginCheck(Symbol(),0,LotGroesse)>1) //Ausreichend Margin?
              {
               if(StopLossVerwenden==true)
                 {
                  SLTemp=Ask+StopLoss*Point;
                    }else{
                  SLTemp=0.0;
                 }

               if(TakeProfitVerwenden==true)
                 {
                  TPTemp=Ask-TakeProfit*Point;
                    }else{
                  TPTemp=0.0;
                 }

               if(OrderSend(Symbol(),OP_SELL,LotGroesse,Bid,0,SLTemp,TPTemp,"Tripple MA Strategie",12345,0,clrNONE)==true)
                 {
                  Alert("Short Position (Lot:"+LotGroesse+"|SL:"+SLTemp+"|TP:"+TPTemp+") eröffnet!");
                 }
                 }else{
               Alert("Keine freie Margin nach Positionseröffnung!");
              }
           }
        }
     }
   if(OrdersTotal()>0)
     {
      if(FilterMAWert0>FilterMAWert1)
        {
         if(MACD_Hauptlinie1<MACD_Signallinie1 && MACD_Hauptlinie0>MACD_Signallinie0) //Gegensignalsignal bullisch -> Schliesse Short
           {
            for(int a=0;a<OrdersTotal();a++)
              {
               if(OrderSelect(a,SELECT_BY_POS,MODE_TRADES))
                 {
                  if(OrderType()==1)
                    {
                     OrderClose(OrderTicket(),OrderLots(),Ask,0,clrNONE);
                    }
                 }
              }
           }
           }else{
         if(MACD_Hauptlinie1>MACD_Signallinie1 && MACD_Hauptlinie0<MACD_Signallinie0)//Gegensignal bearisch -> Schliesse Long
           {
            for(int b=0;b<OrdersTotal();b++)
              {
               if(OrderSelect(b,SELECT_BY_POS,MODE_TRADES))
                 {
                  if(OrderType()==0)
                    {
                     OrderClose(OrderTicket(),OrderLots(),Bid,0,clrNONE);
                    }
                 }
              }
           }
        }
      if(StopLossNachziehen==true)
        {
         for(int c=0;c<OrdersTotal();c++)
           {
            if(OrderSelect(c,SELECT_BY_POS,MODE_TRADES))
              {
               if(OrderType()==0)
                 {
                  if(Ask>OrderStopLoss()+StopLossSchritt*Point+StopLoss*Point)
                    {
                     NeuSL=OrderStopLoss()+StopLossSchritt*Point;
                     OrderModify(OrderTicket(),OrderOpenPrice(),NeuSL,OrderTakeProfit(),0,clrNONE);
                    }
                 }
               if(OrderType()==1)
                 {
                  if(Bid<OrderStopLoss()-StopLossSchritt*Point-StopLoss*Point)
                    {
                     NeuSL=OrderStopLoss()-StopLossSchritt*Point;
                     OrderModify(OrderTicket(),OrderOpenPrice(),NeuSL,OrderTakeProfit(),0,clrNONE);
                    }
                 }
              }
           }
        }
     }
  }
