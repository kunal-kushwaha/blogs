Developers face many problems in integrating all the tools in a single place, as it is time-consuming and slows down the process of delivering the software from code to production.Like many tools exist in the market to solve the problem of cloud deployment. There are different tools for containerization, others for deployment and more for monitoring, security, scaling and testing. But what if there is a platform which helps developers to integrate and use all the tools in a single place. In this blog, I have covered developers common challenges and how one such tool [Choreo](https://choreo.dev/) addresses these issues, and walk through an end-to-end demo showcasing the platform’s capabilities.

## Common Challenges in Cloud Native Deployment

Developers face various challenges in application deployment. Let's discuss some of them:

1. **Observability Challenges**

 Identifying and fixing issues in production is extremely difficult and sometimes it generates lots of loss in business. Developers can use Observability tools to fix that. For example, a lack of proper monitoring led to an outage of Twitter's service in 2016.
 
2. **Deployment Difficulties**

  Deploying applications without errors is very challenging. Manual deployments generally create errors and sometimes cause significant downtime. In 2019, the Night Capital Group lost **$440 million** in under 45 minutes due to the wrong deployment script.

3. **Scaling Issues**

 When there is an increase in demand for the application, scaling makes it difficult to manage higher loads without comprising the performance becomes difficult. The Best Buy website crashed on Black Friday 2014, due to unexpected traffic increases.

4. **Integration Complexities**

 Integrating many services, APIs, and databases can be difficult sometimes due to compatibility issues.

## Introduction to Choreo: A Solution to Common Deployment Challenges

![Screenshot 2024-08-23 at 12.35.07 AM](https://hackmd.io/_uploads/r1Ixh-riA.png)

Choreo is an internal developer platform that redefines how you create digital experiences. It helps you to design, develop, deploy and govern Cloud-native applications efficiently. Let’s see how Choreo addresses the challenges mentioned above:

1. **Integration**
Choreo supports many services and tools which makes easy for developers to integrate various services together. The platform provides built-in support for wide range of cloud services, databases, and APIs, which allows developers to connect different components of the application faster. It speeds up deployment and reduces the possibility of errors.

2. **Deployment**
Developers may effectively deploy apps across multiple environments using Choreo's simplified CI/CD experience. An application is built just once for each commit on the platform, and the resulting container is then moved to production. This is known as the **build once, deploy many** approach. Moreover, changes are automatically merged without the need for human interaction due to Choreo's support for automatic builds and deploys. Secrets and configurations are safely maintained in the environment

3. **Scalability**
Choreo automatically scales your applications based on demand, which ensures it can handle increased traffic without issues. Its scalable infrastructure adjusts resources that provides the necessary compute power and maintains good performance.

4. **Observability**
Choreo offers performance predictions, anomaly detection, and detailed logs to help you troubleshoot and debug issues effectively. Integrating observability into the deployment lifecycle enables developers to troubleshoot the applications timely which ensures continuous improvement and stability of the application.

5. **Security**
Choreo uses a Zero Trust security model, which is based on the principle of **Never Trust, Always Verify.** This model ensures that all access requests to backend services, internal or external is verified and authorised before they are given. This technique reduces the danger of unauthorised entry through the use of strong identity verification and access controls at all levels of the platform.

### Using Choreo for Application Deployment

In this section I will cover how you can perform various operations with your applications using choreo such as - building, deploying, running, testing and monitoring an application. 

For this demo I will deploying a web application which functions a multi-user book list manager. It allows multiple users to maintain their own book lists independently. Each user can add, remove, and view books in their personal book list. It's built using the Choreo platform, showcasing its capabilities in building and deploying multi-user web applications along with choreo's managed database service.

**Step 1**: **Getting Familiar with Choreo**

Choreo offers multiple functanalities and it's better to filter out the ones required for your application to run without any downtime.

Head over to [choreo docs](https://wso2.com/choreo/docs/) to find your use case. Once you have found the perfect use case go to [choreo console](https://console.choreo.dev/) and sign in to create a organization.

**Step 2**: **Starting the Database Service for the Application**

Once you have created an organization, head over to choreo console and using sidebar choose **Database** option under **Dependencies** tab to start a new database server.

![image](https://hackmd.io/_uploads/HkDMZlrjR.png)

Choose the type of database and allocate a name to your database server. Select the cloud provider, region and desired service plan. Click on create to start the server.

![image](https://hackmd.io/_uploads/r1V8zxHsR.png)

Wait for few minutes for the database service to start, once the database server is started you will be able to see your database server details as below

![image](https://hackmd.io/_uploads/HkKA5lBi0.png)

**Step 3**: **Create a Backend Component**

After creating the database server, head over to organization and click on **Create Project** option.

![image](https://github.com/user-attachments/assets/140c8673-cd9d-4f6c-989c-3fec8db9b80c)

After creating the project you will land on **Component** page, go ahead and create a new **Service** component.

![image](https://hackmd.io/_uploads/B10UT1ri0.png)

Sync your GitHub repository contaning the backend code within this component and click on **Create** button at the bottom.

![image](https://hackmd.io/_uploads/SkYx0kHjR.png)

Once the component is created, go to **Build** tab using side bar and build the desired commit made on your repository.

![image](https://hackmd.io/_uploads/rJ4L0JSoC.png)

Once the build is completed you can now deploy your application using choreo, using sidebar head over to **Deploy** tab. You can either deploy directly or use **Configure and Deploy** option to configure **Environment Variables**, **Secrets** and **File Mounts** before deploying. In this scenario we will be using environment variables and secrets to store our database details. 

Add environment variable by copying necessary details from the database server created in step 2, click on next  

![image](https://hackmd.io/_uploads/SJKfVlrsC.png)

Add a file mount if needed, and in the last step click on deploy button to deploy the service. You will see the **Deployment Status** as **Active** once the service is deployed. You add, delete or edit your configs and secrets and also promote the deployment to production.

![image](https://hackmd.io/_uploads/BJSy8eroA.png)

Let's go ahead and test our newly created service, using sidebar go to the **Console** tab under **Test** section to test endpoint of your backend API service.

![image](https://hackmd.io/_uploads/HJK8IxrjA.png)

Let's go ahead and make a POST request to our service to check the working, click on **POST** section followed by clicking **Try it out** button. Fill in the details and click on **Execute** button to send the post request.

![image](https://hackmd.io/_uploads/r1LluxBi0.png)

Since I am using MySQL in this scenario I will be using [MySQLWorkbench](https://mysql.com/products/workbench/) to check the data stored in the database through the **POST** request. You can either simply make a **GET** request to retrieve the data or head over to [Choreo-Managed Databases and Caches Docs](https://wso2.com/choreo/docs/manage-databases-and-caches/choreo-managed-databases-and-caches/) to make connection with your database server.

![image](https://hackmd.io/_uploads/ry6_cgrsC.png)

**Step 4**: **Create a Frontend Component**

Head back to the project and click on create new component option. Choose **Web Application** from the option

![image](https://hackmd.io/_uploads/rJhYilrsA.png)

After selecting Web Appliation as component type, fill in the necessary details and sync your GitHub repository. Provide details about the **Build Command**, **Build Path**, **Node Version**, and click on **Create** button at the bottom.

![image](https://hackmd.io/_uploads/S1WmEZri0.png)

Once the component is created establish the connection between this component and the component created for the backend service. Using the sidebar go to **Connections** tab under **Dependencies** section.

![image](https://hackmd.io/_uploads/ByvHEZSoC.png)

After making the connection you will land on a page consisting of some details which we will be using while deploying our frontend component.

![image](https://hackmd.io/_uploads/HJSQBbHoA.png)


Now you can again go ahead and build the component using the desired commit made on your Github repository as documented in the above step. 

![image](https://hackmd.io/_uploads/SyHtHWHiA.png)

Head over to deploy tab and now when you click on **Configure and Deploy** option you will be asked to enter some details. Copy the config details from the connections made in the previous section and paste it here to add **config.js** as a **File Mount** and completing the connection between our frontend and backend service.

Click on next and add a user. Make sure to store these **Username** and **Password** generated as you will be using this to login into the application and click on deploy.

![image](https://hackmd.io/_uploads/r1EdvWSiC.png)

After deploying the application visit the **Web App URL** link, and click on **login** button

![image](https://hackmd.io/_uploads/HkIEuZriA.png)

Enter the Username and Password generated while deploying the component in the login form to access the web application

![image](https://hackmd.io/_uploads/HkdJtbSjC.png)

You can now perform all the desired operation using frontend component

![image](https://hackmd.io/_uploads/BJh7KWSjC.png)

**Step 5**: **Checking the Application Observability**

Using the sidebar you can head over to **Observability** section to check the logs and metrics of your components. You can navigate to different options and apply different filters to get the desired output.

**Logs**
![image](https://hackmd.io/_uploads/rJbHiZSsC.png)

**Metrics**
![image](https://hackmd.io/_uploads/rk19o-SoA.png)

You can also look into usage insights of your application
![image](https://hackmd.io/_uploads/SJlQ3ZHsR.png)

Lastly you can also check the delivery insights of your application
![image](https://hackmd.io/_uploads/BJbsn-Bo0.png)

## Key Features of Choreo

1.  **Hosting**
 Choreo makes it easy to deploy and manage Software as a Service (SaaS) applications in the cloud. Whether you're building tools for customers or internal use, Choreo provides the infrastructure and tools to host your SaaS products reliably. Let's say if you're developing a web application you are easily able to host your application as it supports hosting web apps built with various frameworks and languages and ensures that your application is accessible, having good performance and scalable.

2.  **CI/CD (Continuous Integration/Continuous Deployment)**
If you want to manage your multiple versions of your application? No problem. Choreo supports multi-version development which allows you to maintain and deploy different versions of your app simultaneously. This is particularly useful for rolling out new features gradually or supporting legacy users.

3. **Management**
Choreo makes it easy to organize and manage multiple projects and their components within a single platform. This is especially useful for teams working on large, complex applications with many moving parts.Collaboration is key, and Choreo offers tools for managing team roles, permissions, and workflows. This ensures that everyone has the access they need, without compromising security or efficiency.

## Real-World Use Cases and Benefits of Choreo

Choreo’s ability to streamline deployment processes has several real-world applications, offering significant benefits:

1. **Streamlined Development**
   Developers can code in their preferred languages and use native support for VS Code and GitHub which enhanced collaboration and productivity.

2. **Faster Time to Market**
   Choreo’s automation tools reduced the deployment cycle which allows businesses to launch new features rapidly.


3. **Enhanced Governance**
   Choreo enables you to monitor your applications and improve your DevOps processes using DORA metrics and API analytics.

## Why Choose Choreo?

Choreo stands out for various reason in cloud deployment space:

- **User Authentication and Authorization:** Choreo supports built-in user authentication and authorization management which makes easy to secure your applications.
- **Autoscaling and Multi-Environment Support:** Choreo ability to autoscale apps and support multiple environments ensures your applications can handle varying loads without manual intervention.
- **Docker-Based Development:** Choreo allows you to use Docker-based development, which is crucial for modern cloud-native applications.
- **API Management and Marketplace:** Choreo’s API management features and marketplace make it easy to reuse APIs and accelerate development.

## Case Studies

1. **IIIT Hyderabad** used Choreo to build a centralized platform that allowed nonprofit organizations to quickly access services and APIs without having to identify the source organizations. Additionally, Choreo manages infrastructure and enables single sign-on, freeing up team members to concentrate on developing new solutions more quickly.

2. Choreo also provided **cost optimization** for **startups** by providing an effective platform for application deployment.

3. *Cut+Dry* leveraged Choreo to seamlessly integrate with ERPs, overcoming startup challenges with speed, support, and scalability, while balancing open-source flexibility and SaaS efficiency.

## Getting Involved

- Read the [documentation](https://wso2.com/choreo/docs/) in order to learn more.
- **Try Choreo**: Try out the tool from their [official website](https://choreo.dev/) and start building  your application with ease.
- **Join the Community**: Got questions or feedback? Join the Choreo [Discord ](https://discord.com/invite/wso2)community to connect with fellow developers, share insights, and access various to enhance your cloud journey with Choreo. 

## Conclusion

Choreo is a powerful platform that addresses the complexities of cloud-native deployment by providing a comprehensive set of tools for integration, deployment, security, scalability, and observability. By simplifying these processes, Choreo allows developers to focus on what matters most which is building applications rather than manually integration  all the tools and services.
