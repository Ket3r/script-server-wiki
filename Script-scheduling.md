Script server has a possibility to schedule a script for future execution, either once or repeatedly.  
By default this feature is disabled, since many scripts are useful only on demand (for example, password generation).  
You can enable scheduling for a script using the following configuration: Configuration: https://github.com/bugy/script-server/wiki/Script-config#scheduling  
  
When scheduling is enabled, users will see a "schedule" button near "execute" button.  
![](https://user-images.githubusercontent.com/1275813/107157228-438edb00-6983-11eb-8b00-1fed82945453.png)  
By using this button, they can schedule one-time execution in the future or repeatedly.  
![](https://user-images.githubusercontent.com/1275813/107157247-56091480-6983-11eb-95c7-078949bd5320.png) ![](https://user-images.githubusercontent.com/1275813/107157269-79cc5a80-6983-11eb-8a14-f2848d52ef0d.png)  

The execution will be performed with parameter values selected by the user, before pressing "schedule" button.  

Scheduled scripts are stored in `conf/schedules` folder.  

If script configuration is changed and scheduled execution is not valid anymore (e.g. missing new required values), then scheduled execution will be skipped.  
