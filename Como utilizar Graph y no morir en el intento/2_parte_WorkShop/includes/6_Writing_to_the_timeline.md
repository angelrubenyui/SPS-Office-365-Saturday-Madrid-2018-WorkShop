# Writing to the Timeline
----------

## Timeline

MS Graph has a new feature: **UserActivity**.  
User Activities generated by your application appear in Timeline. By writing User Activities into the MS Graph, you can express specific content within your application as a destination which is showcased in Windows, and accessible on your iOS and Android devices.

Each User Activity represents a single destination within your app. When you engage with that activity (by creating an Activity Session), the system creates a history record indicating the start and end time for that activity. As you re-engage with that User Activity over time, multiple History Records will be recorded for a single User Activity.

When activities appear in Timeline, we display them using the [Adaptive Card](http://adaptivecards.io/) framework. You can configure these cards or if you do not provide an adaptive card, Timeline will automatically create a simple Adaptive card based in your activity. 

## Create and Save an Activity in Graph

In the UWP project go to **Helpers/UserExtensionHelper.cs** CreateActivity method and follow the steps:

- Delete the code

	`throw new NotImplementedException();`

- Add the following code
       	
            try
            {           
                UserActivitySession currentActivity;
                UserActivityChannel channel = UserActivityChannel.GetDefault();

                UserActivity userActivity = await channel.GetOrCreateUserActivityAsync("HOLMicroGraph");

                var adaptiveCard = File.ReadAllText($@"{Package.Current.InstalledLocation.Path}\AdaptiveCard.json");
                adaptiveCard = adaptiveCard.Replace("{{backgroundImage}}", "https://cdn.graph.office.net/prod/GraphDocuments/en-us/concepts/images/microsoft_graph.png");
                adaptiveCard = adaptiveCard.Replace("{{name}}", "Hands-on Lab MS Graph");
                userActivity.VisualElements.DisplayText = "HOL MS Graph";
                userActivity.ActivationUri = new Uri($"holmicrosoftgraph://{UserExtensionHelper.option}");
                userActivity.VisualElements.Content = AdaptiveCardBuilder.CreateAdaptiveCardFromJson(adaptiveCard);

                await userActivity.SaveAsync();
                //currentActivity = userActivity.CreateSession();
            }
            catch (Exception ex)
            {
                Debug.WriteLine("Error to create activity in graph: " + ex.Message);
                throw;
            }        


## Create a Session in Graph

Well, now we have saved an activity but we haven't created a session. A user session activity for the user is needed to save the state of the app.

In the code before uncomment the line

	//currentActivity = userActivity.CreateSession();

- Build and run the application.

- Click the Log in button.

- Select **Add in Timeline** option in menu

- Click on Create Activity.

![alt text](../media/CreateActivity.png) 

Now we go to the task view in windows.

![alt text](../media/TaskView.png) 


And we can see the applications in Timeline. If we close the app and click on the Timeline app card, the application will be reopened.

![alt text](../media/Timeline.png) 


## Deep Link

At this point, the application opens to the Login init page.
If we want continue working where we left, we need pass arguments to our application.
In the previous module, when we created the Activity we used this line:

 	userActivity.ActivationUri = new Uri($"holmicrosoftgraph://{UserExtensionHelper.option}");

ActivationUri is optional and at this point is always empty. Go to save where we are.

In the UWP project go to **Helpers/UserExtensionHelper.cs** PickupWhereYouLeft method and follow these steps:

- Add the code


			try
            {
                UserExtensionHelper.option = pickUpOption;
                await UserExtensionHelper.CreateActivity();
            }
            catch (Exception ex)
            {
                Debug.WriteLine("Error to create activity in graph: " + ex.Message);
                throw;
            }

- Build and run the application.

- Click the Log in button.

- Select any option.

- Close the application.

- Open Cortana and go to Pick up where you left off.

- Open the app.

- The app opens where we are.

We can save the state of the application to OneDrive as seen in previous sections.

> **Cross Platform:** Thanks to MS Graph Activities we can open the application in Android or iOS at the same point that left off in our UWP. If you want learn more about how do it visit this [link](https://github.com/Microsoft/project-rome).

> **Note:** For Timeline we use **Adaptive Cards** to show the app in Timeline. You can learn more about **Adaptive Cards** [here](http://adaptivecards.io/).


# Cortana integration

Timeline helps the user to pick up where they left off working. When you create an activity and session, Cortana displays applications to continue working on.

## Pick up where you left off

Close the application and open **Cortana** to see:


![alt text](../media/Cortana.png) 

If we click the application it opens to the previous state