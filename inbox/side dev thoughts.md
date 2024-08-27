
- [ ] - electron.js for cross platform apps (Obsidian based on it) 
- [ ] - can we add to whole dev workflow common tasks (like a `PriorQueue<Task>`) for devs do not wait analytics, architects etc. just to take tasks from *common tasks queue* - like an *event loop* approach and work like an one big team without dividing
- [ ] - WordPress(Linux + Apache/Nginx + MySQL/Postgresql + PHP) is CMS to manage site content via simple UI with various plugins, themes etc. WP can be used as simple site constructor with default templates and advanced settings instead of using NextJs/React etc.
- [ ] - *Serverless function*: 
- In a serverless world, or at least the one we are discussing today you **aren’t dealing with an entire applications and instead focused on individual functions**. This is why you will often hear serverless referred to as serverless functions or **Functions as a Service (FaaS)**. As you have probably already guessed there are servers involved, the serverless part in the name just implies that you don’t have to provision them or care about who has
- What happens in a scenario where you aren’t taking requests all day? Let’s say you created a new landing page for a newsletter that you started. On this landing page subscribers could sign up to receive your weekly newsletter by entering their email address and clicking the sign-up button. I currently have about 4,000 subscribers on my newsletter and at best get about 5 new subscribers per day. **In this situation does it really make sense to pay for a server to be running all day long to handle 5 requests**?
- Spring Cloud Function
```java title:example.java
@Bean public Function<String, String> uppercase() { 
    return value -> value.toUpperCase(); 
}
```
> If you run the application you can visit the URL `http://localhost:8080/uppercase/foo` where the first parameter is the name of the function and 2nd is the value you are passing to it. In this simple smoke test you should see the value _foo_ in all uppercase displayed in the browser window.edited 00:05
- [CloudeFlare Workers](https://workers.cloudflare.com/) is serverless function. 
```javascript title:example.js
export default {

	async fetch(request, env, ctx) {
	
		const url = "https://jsonplaceholder.typicode.com/todos/1";
	
		async function gatherResponse(response) {
			const { headers } = response;
			const contentType = headers.get("content-type") || "";
			if (contentType.includes("application/json")) {
				return { 
					contentType, result: JSON.stringify(await response.json()) 
				};
			}
			return { contentType, result: response.text() };
		}
	
			const response = await fetch(url);
			const { contentType, result } = await gatherResponse(response);
			const options = { headers: { "content-type": contentType } };
			return new Response(result, options);
	},
};
```