time_limit=120; //in minutes

mail_id=1;
to = "support@test.com,ss@test.com"; // Recepient address

from = "test@axxonsoft.com"; // Sender address
cc="";
dep=1;


if (Event.SourceType == "TIMER" && Event.Action == "TRIGGER" && Event.SourceId=="1")
{

msg=CreateMsg();
 msg.StringToMsg(GetObjectIds("PERSON"));
 var objCount=msg.GetParam("id.count");
 t1=GetCurMin();
 for (i=0;i<objCount;i++)
 {
 pid=msg.GetParam("id."+i);
 num=GetObjectName("PERSON",pid);
 if (GetObjectParam("PERSON",pid,"parent_id")==dep)
 {
 t2=t1-GetObjectParam("PERSON",pid,"card");

if (t2>time_limit) 
 { 
 DoReactStr("DIALOG","plate1","RUN","plate<"+num+">");
 
 body = ""; //Body text
 subject = "Truck "+num+" is inside the Maintenance area after completing time limit"; // Message's subject
 send_mail("",to,from,cc,subject,body); 
 }
 }
 }


}

 

if (Event.SourceType == "ULPR" && Event.Action == "NUMBER_DETECTED" && Event.SourceId=="1")
{
//DebugLogString("------------------------------------");
number=Event.GetParam("plate");
//DebugLogString(number);
tt=GetCurMin();

NotifyEventStr("CORE","","CREATE_OBJECT","objtype<PERSON>,parent_id<"+dep+">,name<"+number+">,pin<"+number+">,card<"+tt+">");
}

if (Event.SourceType == "ULPR" && Event.Action == "NUMBER_DETECTED" && Event.SourceId=="2")
{

number=Event.GetParam("plate");

pid1=0;
pid1=GetObjectIdByParam("PERSON","pin",number);
//DebugLogString("номер "+number+"----"+pid1);
if (!(pid1==0))
{
//DebugLogString("------------------------------------"+number);
NotifyEventStr("CORE","","DELETE_OBJECT","objtype<PERSON>,parent_id<"+dep+">,objid<"+pid1+">");
}

}


function send_mail(attachment,to,from,cc,subject,body)
{

var m = CreateMsg();
 m.SourceType = "MAIL_MESSAGE";
 m.SourceId = mail_id;
 m.SetParam("attachments",attachment);
 m.SetParam("to",to); // Change it to recipient e-mail
 m.SetParam("from",from);// Change it to the sender's email
 m.SetParam("cc",cc);// Add 'CC' using ',' as a delimiter
 m.SetParam("subject",subject); // Subject text
 m.SetParam("body",body); // Body text
 m.Action = "SETUP";
 DoReact(m);
 if(to!=""){
 m.Action("SEND");
 DoReact(m);
 }

}

function GetCurMin()
{
 var cur_time=new Date();
 cur_time=cur_time.getTime();
 cur_time=cur_time/60000;
 cur_time=parseInt(cur_time); 
 return cur_time;
