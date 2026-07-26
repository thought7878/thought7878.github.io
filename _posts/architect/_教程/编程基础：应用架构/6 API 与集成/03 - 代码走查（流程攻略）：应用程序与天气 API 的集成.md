在应用程序示例中使用外部API的目的是什么？  
答：在应用程序示例中使用外部API的目的是集成和访问来自官方天气提供商的天气数据，这些数据显示在天气web应用程序中。  
  
web应用程序如何使用开放天气API？  
答：web应用程序通过发送带有城市名称的请求来使用开放天气API，以获取当前天气状况。 如果天气数据可用，它会显示温度和描述; 如果城市名称无效，则会显示错误消息。  
  
应用程序.py文件中的代码是做什么的？  
答：应用程序.py文件中的代码设置主路由并处理获取和发布请求。 它使用Flask web框架根据请求类型呈现带或不带天气数据的主HTML页面，并调用开放天气API来检索指定城市的天气信息。  
  
为什么会选择Python而不是Node.js作为后端，哪些库或框架可以简化后端处理？  
答：可能会选择Python而不是Node.js作为后端，因为它易于使用，并且对数据处理和科学计算的强大支持。 使用Flask web框架简化后端处理，这与Node.js的Express相当。  
  
后端应用程序在处理用户请求方面扮演什么角色？  
答：后端应用程序使用Flask web框架用Python编写，通过处理HTTP get和post请求在指定路由上处理用户请求。 它呈现主页面，没有天气或错误数据用于get请求，并显示输入的城市与天气详细信息或错误消息用于发布请求。

---

00:00 使用Python和开放天气API构建天气应用  
学习使用Python的Flask框架和开放的天气API创建天气应用程序，演示外部APIs如何增强功能和数据集成。

---

# 原文
Let's take a look at a practical example of how an external API can be used in an application. We'll download our dependencies with Pip and then use Python to run it in the terminals. If you don't have Python already installed, you can download it at python dot org slash downloads dot. If you're on Windows, the command is just Python and not Python 3. Let's run it. Our application is running and it's located on port 5000. Let's open it up in a web page. 

**让我们来看一个实际案例，说明如何在应用程序中使用外部API**。我们将使用Pip下载依赖，然后在终端中用Python运行它。如果您尚未安装Python，可以从python.org/downloads下载。如果你使用的是Windows，命令只需输入Python，而无需指定为Python 3。我们运行它吧。我们的应用程序正在运行，它监听在5000端口上。让我们在网页中打开它。

Here we have a weather web application that tells us the weather of a given city. Let's check the weather for Philadelphia. It looks like it's 75 degrees in Philly. Now, our application doesn't calculate this weather data, it's actually pulled from an official weather provider through the open weather API. Let's see how it works under the hood. 

这里有一个天气Web应用程序，它可以为我们提供指定城市的天气信息。我们来看看费城的天气吧。费城现在气温大约是75华氏度。目前，我们的应用并不会自行计算这些气象数据，而是通过OpenWeather API从官方气象服务商获取的。让我们看看它的底层实现。

This application consists of a simple HTML form where users can enter a city name and get the current weather conditions. This form sends a post request to our server when submitted, passing the city name as a parameter. If the weather is available, it shows the weather details for the specified city, including the temperature and description. If there's an error like an invalid city name, it displays the error message instead. 

此应用程序包含一个简单的HTML表单，用户可以在其中输入城市名称并获取当前的天气状况。此表单在提交时会向我们的服务器发送post请求，并将城市名称作为参数传递。如果天气可用，它会显示指定城市的天气详细信息，包括温度和描述。如果出现错误，如城市名称无效，它会显示错误消息。

This is using Jenga 2 template syntax to conditionally render content based on the server's response. For the styling of our application, we use a simple CSS file. Now, the real magic happens in the app dot py file. 

这是使用Jenga 2模板语法，根据服务器的响应有条件地呈现内容。对于我们应用程序的样式，我们使用一个简单的CSS文件。现在，真正的魔力发生在应用程序的.py文件中。

This is our backend. In previous videos, we've used node JS for our backend, but both Python and Nodejs can effectively manage server side processing. Python is often chosen for its ease of use and strong support for data manipulation and scientific computing, while Nodejs is favored for its non blocking architecture and scalability. In this case, we use Python to show an alternative to Node JS for back-end coded. 

这是我们的后端。在之前的视频中，我们曾使用Node.js作为后端，但*Python和Node.js都能高效地处理服务器端逻辑。Python通常因其易用性以及对数据处理和科学计算的强大支持而备受青睐，而Node.js则因其非阻塞的架构和良好的可扩展性而广受推崇*。在这种情况下，我们使用Python作为Node.js的后端替代方案。

Here we use the Flask web framework to simplify handling Http requests and routing. Think of Flasks like Express for Node JS. With this code, we set up the home route and allow get and post requests on it. When we render the main page of the application, we're making a get request. So we just render the HTML page with no weather or error data. When we fill out the form with the city and press submit, we make a post request to this same route. 

在这里，我们使用Flask Web框架来简化HTTP请求的处理与路由。可以把Flask看作是Node.js的Express。使用此代码，我们设置了主路由并允许在其上获取和发布请求。当我们渲染应用程序的主页面时，我们正在发出get请求。因此，我们只是渲染不含天气或错误数据的HTML页面。当我们填写城市信息并提交表单时，会向该路由发起一个POST请求。

In the code. We retrieves the city and then call the Open Weather API using the request module. Once we get the response, we check it was successful and then set up the weather object with the appropriate city temperature and description. Then we render the HTML page. With this application, it's easy to see how Apis can enhance it by making it easier to access and integrate external information.

在代码中。我们先获取城市信息，然后使用request模块调用OpenWeather API。收到响应后，我们先检查请求是否成功，然后使用相应的城市气温和天气描述来初始化天气对象。然后我们渲染HTML页面。

*借助这款应用程序，不难看出Apis如何通过简化对外部信息的访问与集成来实现其价值提升*。

