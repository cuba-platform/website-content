1
00:00:00,433 --> 00:00:05,199
Hello! And welcome to the CUBA Platform Quick Start Video.

2
00:00:05,200 --> 00:00:10,366
CUBA Platform is an open source framework based on the well-known Spring framework.

3
00:00:10,366 --> 00:00:15,566
The purpose of CUBA is to streamline the development process of business applications.

4
00:00:15,566 --> 00:00:18,899
This quick start video covers the following topics:

5
00:00:19,633 --> 00:00:21,799
data model and database creation,

6
00:00:23,666 --> 00:00:26,566
user interface development and customization

7
00:00:29,333 --> 00:00:31,933
and business logic implementation.

8
00:00:36,166 --> 00:00:41,032
As an example, we will create a simple, but fully functional version of an application

9
00:00:41,033 --> 00:00:43,866
that will help with session planning for a conference.

10
00:00:43,866 --> 00:00:47,799
The video will be just enough for you to start developing with CUBA.

11
00:00:49,133 --> 00:00:53,033
For this video, in addition to the CUBA Platform framework,

12
00:00:53,033 --> 00:00:57,499
we will use a plugin for IntelliJ Idea  - CUBA Studio.

13
00:00:58,900 --> 00:01:04,366
Let’s create an empty CUBA project with the project namespace - planner.

14
00:01:04,366 --> 00:01:08,299
We will use Java 11 as the default JDK

15
00:01:08,300 --> 00:01:10,733
and the latest stable CUBA version.

16
00:01:12,033 --> 00:01:16,099
We will use an in-memory database - HSQLDB.

17
00:01:16,100 --> 00:01:18,333
This should be enough for the quick start.

18
00:01:18,333 --> 00:01:21,466
You can select another database from the drop-down list.

19
00:01:21,466 --> 00:01:24,566
Leave database connection parameters unchanged.

20
00:01:25,200 --> 00:01:27,833
The project name will be Session Planner.

21
00:01:30,666 --> 00:01:37,699
After the first launch CUBA Studio offers you to apply a trial subscription to CUBA Studio Premium.

22
00:01:37,700 --> 00:01:42,933
 It is free for 28 days and gives us some useful optional designers.

23
00:01:42,933 --> 00:01:44,066
Let’s apply it.

24
00:01:45,700 --> 00:01:51,433
On the left side of the screen you can see the CUBA Project navigation tree.

25
00:01:51,433 --> 00:01:53,699
In the center there is a Welcome page

26
00:01:53,700 --> 00:01:58,866
and you can use it for quick access to some CUBA functions, documentation, marketplace,

27
00:01:58,866 --> 00:02:02,766
and also versions and subscription information.

28
00:02:02,766 --> 00:02:06,966
On the right side of the screen you can see the Gradle Tool window.

29
00:02:06,966 --> 00:02:10,999
CUBA framework uses Gradle to build and run the application.

30
00:02:12,233 --> 00:02:16,533
The standard IntelliJ’s build window is at the bottom.

31
00:02:16,533 --> 00:02:20,333
We can close some windows to get some space on the screen.

32
00:02:20,333 --> 00:02:25,966
The CUBA main menu provides quick access to the CUBA-specific features of the IDE.

33
00:02:28,733 --> 00:02:32,866
Now we will create JPA classes along with business rules

34
00:02:32,866 --> 00:02:36,066
like mandatory fields, unique fields, and relations.

35
00:02:37,566 --> 00:02:46,099
The business domain model has only two classes (also called entities): Speaker and Session.

36
00:02:46,100 --> 00:02:50,933
To simplify the development process we added a visual designer to CUBA Studio

37
00:02:50,933 --> 00:02:54,333
that makes JPA entities creation easier.

38
00:02:54,333 --> 00:02:58,899
Right click on the “Data Model” and select “New -> Entity”.

39
00:02:58,900 --> 00:03:01,900
First, we need to create the Speaker entity.

40
00:03:03,000 --> 00:03:04,966
It has three fields -

41
00:03:04,966 --> 00:03:07,066
first name which is mandatory,

42
00:03:09,366 --> 00:03:12,332
last name - optional,

43
00:03:12,600 --> 00:03:16,833
and email - mandatory and unique.

44
00:03:16,833 --> 00:03:20,699
Let’s add validation for the email field.

45
00:03:20,700 --> 00:03:24,933
CUBA provides an ability to specify a proper entity display

46
00:03:24,933 --> 00:03:28,833
in the string format in the UI - “Instance Name”.

47
00:03:28,833 --> 00:03:32,999
For the speaker, we will select both the first and the last names.

48
00:03:33,000 --> 00:03:35,966
Let’s have a closer look at the entity designer.

49
00:03:35,966 --> 00:03:39,566
Next to the designer tab we can see generated text -

50
00:03:39,566 --> 00:03:42,366
JPA-annotated java class.

51
00:03:42,366 --> 00:03:45,132
They are integrated, so you can change the text

52
00:03:45,133 --> 00:03:48,033
and see all the changes in the designer and vice versa.

53
00:03:48,033 --> 00:03:50,266
Also, you can see the DDL Preview

54
00:03:50,266 --> 00:03:52,932
and can create indexes if required.

55
00:03:52,933 --> 00:03:56,366
For instance, we expect a lot of searches by last name.

56
00:03:56,366 --> 00:04:01,499
To make this search more efficient we can create an index for the last name field.

57
00:04:08,400 --> 00:04:11,533
Let’s move further and create the Session entity

58
00:04:11,533 --> 00:04:14,033
and link it to our Speaker class.

59
00:04:14,033 --> 00:04:18,766
First, define the attribute Topic - a mandatory string.

60
00:04:18,766 --> 00:04:22,766
After that, we will add the session start date and the time attribute.

61
00:04:22,766 --> 00:04:26,299
Add the duration of the session with validation.

62
00:04:30,300 --> 00:04:34,366
The session end date and time will be a calculated value

63
00:04:34,366 --> 00:04:36,266
that we’ll configure it a bit later.

64
00:04:36,266 --> 00:04:39,832
Now we’ll add a mandatory reference to the speaker.

65
00:04:39,833 --> 00:04:41,499
The relation is many-to-one,

66
00:04:41,500 --> 00:04:44,700
so, we’ll define an association field called speaker

67
00:04:44,700 --> 00:04:48,533
that references the speaker class.

68
00:04:48,533 --> 00:04:51,566
In addition to this, we’ll create a description field

69
00:04:51,566 --> 00:04:54,932
that will contain a session’s long description.

70
00:04:54,933 --> 00:04:59,266
For the instance name we will select a topic field instead of the description.

71
00:05:02,500 --> 00:05:08,400
Now, we need to create an auto calculated attribute for the session’s end date and time.

72
00:05:12,600 --> 00:05:15,100
First add the getter method

73
00:05:15,100 --> 00:05:18,466
and name it getEndDate.

74
00:05:18,466 --> 00:05:21,966
Mark the method with the @MetaProperty annotation.

75
00:05:21,966 --> 00:05:26,332
For the calculated attribute, we need to specify the fields to be loaded.

76
00:05:26,333 --> 00:05:29,733
In our case, these are startDate and duration.

77
00:05:29,733 --> 00:05:32,633
Then just add calculation logic.

78
00:05:36,133 --> 00:05:38,666
Notice that Studio highlights the method

79
00:05:38,666 --> 00:05:42,666
because we need to specify a text label for the attribute.

80
00:05:42,666 --> 00:05:44,699
Let’s add it to the message bundle.

81
00:05:47,100 --> 00:05:50,100
Now, go back to the designer and have a look.

82
00:05:50,100 --> 00:05:52,866
We see that the endDate field is added

83
00:05:52,866 --> 00:05:57,299
and it is read only and related attributes are specified.

84
00:05:57,300 --> 00:05:59,633
Our domain model has been created.

85
00:06:01,800 --> 00:06:05,433
Now, we can create a simple UI to perform CRUD operations

86
00:06:05,433 --> 00:06:07,499
over the tables in the database.

87
00:06:11,066 --> 00:06:15,199
CUBA Studio contains a UI screen generation wizard

88
00:06:15,200 --> 00:06:19,000
that helps us to create basic, but useful UI screens:

89
00:06:19,000 --> 00:06:23,066
the Browser - to show the list of entities in a grid

90
00:06:23,066 --> 00:06:30,332
and the Editor - to edit one entity instance using a form-like user interface.

91
00:06:30,333 --> 00:06:34,066
First, we will create screens to work with speakers.

92
00:06:34,066 --> 00:06:36,199
Since this entity is pretty simple,

93
00:06:36,200 --> 00:06:40,466
we can accept the default parameters for the screen creation.

94
00:06:40,466 --> 00:06:44,499
As you can see, each screen consists of two parts:

95
00:06:44,500 --> 00:06:48,566
XML layout that defines the screen appearance with the preview feature,

96
00:06:50,400 --> 00:06:52,533
and a controller, written in java,

97
00:06:52,533 --> 00:06:56,533
which is responsible for internal screen logic and events handling.

98
00:06:56,533 --> 00:06:59,533
Let’s take a look at the structure of the screen’s layout:

99
00:06:59,533 --> 00:07:01,433
there are two main blocks.

100
00:07:01,433 --> 00:07:05,533
The first one defines the way we are working with data

101
00:07:05,533 --> 00:07:07,866
and the second block defines the  layout

102
00:07:07,866 --> 00:07:11,366
that specifies the positions of the components on the screen.

103
00:07:11,366 --> 00:07:14,866
Now let’s create a browser and an editor for sessions.

104
00:07:18,766 --> 00:07:22,832
 Here we need to pause for a moment and explain some elements of CUBA. 

105
00:07:22,833 --> 00:07:28,433
In the CUBA Platform, Entity View specifies which fields will be fetched from the database. 

106
00:07:28,433 --> 00:07:33,199
You can define views in a separate file to use them in the different modules 

107
00:07:33,200 --> 00:07:37,566
of your application, or create inline views while creating the screens. 

108
00:07:37,566 --> 00:07:40,366
So, let’s create an inline view.

109
00:07:40,366 --> 00:07:42,566
Just select the necessary data. 

110
00:07:47,900 --> 00:07:51,666
You can see that the corresponding fields are added to the screens.

111
00:07:54,533 --> 00:08:00,133
In addition, set the default duration value for the new session to one hour. 

112
00:08:00,133 --> 00:08:02,433
To do this, go to the editor screen 

113
00:08:02,433 --> 00:08:07,833
and subscribe to the event InitEntity and set this value in the code.

114
00:08:12,033 --> 00:08:15,666
Now, we need to generate scripts to create the database.

115
00:08:18,200 --> 00:08:21,233
To generate scripts for the database creation,

116
00:08:21,233 --> 00:08:25,833
you need to select the CUBA - Generate Database Scripts menu.

117
00:08:25,833 --> 00:08:28,333
To apply those scripts and create the database,

118
00:08:28,333 --> 00:08:30,733
just click Create database.

119
00:08:30,733 --> 00:08:32,566
Aside from application tables,

120
00:08:32,566 --> 00:08:35,932
CUBA creates system tables where we store information

121
00:08:35,933 --> 00:08:40,466
about users, roles, tasks, and others.

122
00:08:40,466 --> 00:08:44,666
Now we can run the application in development mode from the IDE.

123
00:08:47,833 --> 00:08:52,366
Just press “Run” in the IDE to start the application.

124
00:08:55,566 --> 00:09:00,132
You can see the application log in the studio’s “Run” window.

125
00:09:00,133 --> 00:09:04,499
After some time you can access the application using the browser.

126
00:09:04,500 --> 00:09:07,566
Let’s open the URL and log in to the application

127
00:09:07,566 --> 00:09:09,732
using  the username “admin”.

128
00:09:09,733 --> 00:09:12,866
The password is “admin” by default.

129
00:09:12,866 --> 00:09:15,699
Let’s add some test data to the application:

130
00:09:15,700 --> 00:09:19,200
first, let's add a couple of speakers,

131
00:09:19,200 --> 00:09:22,966
As you can see, email validation works as expected.

132
00:09:27,533 --> 00:09:30,466
Now let’s add sessions for today and tomorrow.

133
00:09:35,433 --> 00:09:38,999
You can see that the endDate is calculated automatically.

134
00:09:57,666 --> 00:10:01,199
Generated screens are good for basic operations 

135
00:10:01,200 --> 00:10:05,400
but in the real world, the UI is usually more complex.

136
00:10:05,400 --> 00:10:11,133
Let’s add a calendar view to browse sessions in addition to the grid view.

137
00:10:11,133 --> 00:10:14,666
For the browser screen, we’ll add tab control,

138
00:10:14,666 --> 00:10:17,699
put a calendar on it and provide functionality

139
00:10:17,700 --> 00:10:23,300
to edit and reschedule sessions using this calendar.

140
00:10:23,300 --> 00:10:29,066
Let’s wrap the sessions table into tab sheet in the sessions browser screen.

141
00:10:29,066 --> 00:10:31,299
Add one more tab page.

142
00:10:31,300 --> 00:10:34,566
and put a calendar control on it.

143
00:10:34,566 --> 00:10:40,166
Expand the TabSheet element to the whole screen.

144
00:10:40,166 --> 00:10:42,632
Studio asks for an id.

145
00:10:42,633 --> 00:10:47,233
In CUBA, we need IDs to reference a screen element in the code.

146
00:10:47,233 --> 00:10:51,033
Also, let’s set id-s and captions for each tab.

147
00:11:06,933 --> 00:11:11,133
Update the calendar - assign a data container and expand it.

148
00:11:13,433 --> 00:11:16,899
Finally, expand the Session Table.

149
00:11:16,900 --> 00:11:21,233
In CUBA, UI components are bound to entities and their properties.

150
00:11:21,233 --> 00:11:25,166
We will bind the calendar to the data collection fetched in the screen.

151
00:11:25,166 --> 00:11:28,799
Use the search field to find properties and bind:

152
00:11:28,800 --> 00:11:31,966
startDateProperty to a session’s start Date,

153
00:11:31,966 --> 00:11:35,299
endDateProperty to a session’s end Date,

154
00:11:35,300 --> 00:11:38,200
captionProperty to a session’s topic,

155
00:11:38,200 --> 00:11:41,666
and descriptionProperty to a session’s description.

156
00:11:41,666 --> 00:11:45,799
Let’s make the calendar to show only working hours.

157
00:11:48,333 --> 00:11:53,499
In addition to the visual designer, you can use the XML markup editor.

158
00:11:53,500 --> 00:11:55,766
Let’s add navigation buttons.

159
00:11:58,433 --> 00:12:01,299
We can reopen the form to see the changes -

160
00:12:01,300 --> 00:12:05,000
the CUBA framework supports hot reload for screens.

161
00:12:05,000 --> 00:12:09,766
Now we can see sessions displayed on the calendar.

162
00:12:09,766 --> 00:12:13,166
Now let’s invoke the session editor screen

163
00:12:13,166 --> 00:12:16,799
when we click on an event in the calendar.

164
00:12:16,800 --> 00:12:19,866
When we interact with the UI, events are generated.

165
00:12:19,866 --> 00:12:23,266
We can subscribe to those events to handle them.

166
00:12:23,266 --> 00:12:25,466
Let’s handle a click on a calendar’s entry.

167
00:12:28,400 --> 00:12:32,166
We need to invoke the editor screen to change the session’s properties.

168
00:12:32,166 --> 00:12:34,932
Let’s use the EditorScreenFacet.

169
00:12:34,933 --> 00:12:39,399
It is a component that provides an ability to pre-configure an editor screen.

170
00:12:39,400 --> 00:12:43,400
Put it under the window element in the Component Hierarchy window.

171
00:12:43,400 --> 00:12:46,166
And set the properties:

172
00:12:46,166 --> 00:12:48,499
an ID

173
00:12:48,500 --> 00:12:51,100
 We need an editor for the session class.

174
00:12:51,100 --> 00:12:56,733
Set the Entity class - session and the corresponding data container.

175
00:12:56,733 --> 00:12:59,533
Screen class - SessionEdit.

176
00:12:59,533 --> 00:13:01,899
The screen will be opened in the dialog mode.

177
00:13:01,900 --> 00:13:05,433
Set the edit mode.

178
00:13:05,433 --> 00:13:07,533
Go back to the event handler.

179
00:13:07,533 --> 00:13:11,166
Inject the EditorScreenFacet.

180
00:13:11,166 --> 00:13:15,832
Pass the session entity received within the event object for editing.

181
00:13:15,833 --> 00:13:18,699
After that, we should show the editor.

182
00:13:18,700 --> 00:13:22,633
Let’s reopen the screen and invoke the editor.

183
00:13:22,633 --> 00:13:26,733
As you can see, we need to refine the session editor dialog

184
00:13:26,733 --> 00:13:30,199
to make it look better by adjusting its width and height.

185
00:13:30,200 --> 00:13:34,800
The simplest way to do this is to set “auto” for both width and height.

186
00:13:39,500 --> 00:13:43,000
Let’s reopen the screen again and see the changes.

187
00:13:47,500 --> 00:13:51,133
Now it is time to add some business logic to our application.

188
00:13:51,133 --> 00:13:54,666
In this section, we will use CUBA Studio to create a service

189
00:13:54,666 --> 00:13:56,399
that implements business logic

190
00:13:56,400 --> 00:13:59,000
and use this service in a screen controller.

191
00:13:59,000 --> 00:14:01,500
It will be a service for session rescheduling

192
00:14:01,500 --> 00:14:05,800
that will ensure that one speaker doesn’t have more than two sessions in one day.

193
00:14:05,800 --> 00:14:09,366
Right-click on the service node in the CUBA project tree

194
00:14:09,366 --> 00:14:12,132
and create a SessionService interface

195
00:14:12,133 --> 00:14:14,899
and SessionServiceBean implementation.

196
00:14:14,900 --> 00:14:17,200
Create a method in the interface

197
00:14:17,200 --> 00:14:19,200
and implement it in the ServiceBean.

198
00:14:19,200 --> 00:14:21,133
The method will accept a session

199
00:14:21,133 --> 00:14:23,533
and a new session date and time.

200
00:14:23,533 --> 00:14:26,733
The service will return the updated Session instance.

201
00:14:29,166 --> 00:14:34,166
First, calculate the start and end time of the day where the session is planned.

202
00:14:43,633 --> 00:14:46,699
We'll use  CUBA API for data access.

203
00:14:46,700 --> 00:14:48,333
the DataManager class.

204
00:14:48,333 --> 00:14:51,899
With  DataManager we create a JPQL query

205
00:14:51,900 --> 00:14:56,800
to check if there are any sessions scheduled for the speaker in a defined time span

206
00:14:56,800 --> 00:14:58,766
and add parameter values to it.

207
00:15:08,600 --> 00:15:10,833
Then we check the query result

208
00:15:10,833 --> 00:15:12,099
and depending on the result

209
00:15:12,100 --> 00:15:14,600
we update the session with a new start date

210
00:15:14,600 --> 00:15:18,900
or just return the original session instance.

211
00:15:18,900 --> 00:15:20,200
The service is ready

212
00:15:20,200 --> 00:15:22,700
so let’s add it to the session browser screen.

213
00:15:22,700 --> 00:15:26,866
It will be invoked for the drag-and-drop event in the calendar.

214
00:15:26,866 --> 00:15:30,099
In the method that is subscribed to the drag-and-drop event,

215
00:15:30,100 --> 00:15:33,333
we add some code to extract the session entity

216
00:15:33,333 --> 00:15:36,166
from the calendar event and pass it to the service

217
00:15:36,166 --> 00:15:37,399
to reschedule this session.

218
00:15:41,933 --> 00:15:46,466
After that, we just update this item in the browser’s data container.

219
00:15:48,366 --> 00:15:50,932
To have the new service redeployed,

220
00:15:50,933 --> 00:15:52,833
we need to restart the application.

221
00:15:56,100 --> 00:15:59,100
After restarting we can open the sessions calendar -

222
00:15:59,100 --> 00:16:04,566
and voila! We have drag-and-drop rescheduling functionality for the calendar!

223
00:16:04,566 --> 00:16:05,932
Let’s see how it works.

224
00:16:05,933 --> 00:16:07,833
Just add a couple of extra sessions

225
00:16:19,466 --> 00:16:23,766
and try to reschedule one of the sessions to the day that is already full.

226
00:16:28,566 --> 00:16:32,632
In this section we will customize standard captions in the application,

227
00:16:32,633 --> 00:16:34,199
adding some branding.

228
00:16:35,533 --> 00:16:40,666
In CUBA, you can update resource files and override the standard text.

229
00:16:40,666 --> 00:16:44,966
Let’s update the text according to the application business domain -

230
00:16:44,966 --> 00:16:45,999
conference planning.

231
00:16:48,666 --> 00:16:51,199
With CUBA’s hot deploy feature,

232
00:16:51,200 --> 00:16:55,200
all we need to do is to log in to the application again to see the changes.

233
00:16:57,866 --> 00:17:00,499
The framework comes with a marketplace

234
00:17:00,500 --> 00:17:02,233
that contains plenty of components,

235
00:17:02,233 --> 00:17:04,699
so you can easily add useful features

236
00:17:04,700 --> 00:17:08,533
like charts or maps support to the existing application.

237
00:17:08,533 --> 00:17:11,766
You can install those components right from the studio

238
00:17:11,766 --> 00:17:13,266
using the “Marketplace” menu.

239
00:17:13,266 --> 00:17:15,199
Let’s add the Helium add-on. 

240
00:17:15,200 --> 00:17:19,200
It’s a new visual theme that you can use instead of the standard themes.

241
00:17:19,200 --> 00:17:22,633
Just click install and apply the changes.

242
00:17:22,633 --> 00:17:27,333
Now we need to stop the application and apply the init scripts of the add-on.

243
00:17:29,700 --> 00:17:31,133
Run the application 

244
00:17:33,700 --> 00:17:35,633
and go to the settings screen.

245
00:17:36,633 --> 00:17:39,733
You can find the added theme in the drop-down list.

246
00:17:39,733 --> 00:17:41,633
Select it and apply.

247
00:17:42,333 --> 00:17:46,566
Log in to the application again - theme is already applied.

248
00:17:46,566 --> 00:17:51,799
We can also open the theme settings screen and change settings with preview.

249
00:17:57,933 --> 00:18:01,866
The CUBA framework provides a lot of useful APIs

250
00:18:01,866 --> 00:18:04,666
to help you create business applications quickly.

251
00:18:04,666 --> 00:18:08,166
This quickstart shows only the basics of the CUBA framework

252
00:18:08,166 --> 00:18:09,832
and CUBA Studio.

253
00:18:09,833 --> 00:18:11,999
You can find more examples and tutorials

254
00:18:12,000 --> 00:18:15,366
on our website: cuba-platform.com.

255
00:18:16,633 --> 00:18:18,133
Thank you for watching!