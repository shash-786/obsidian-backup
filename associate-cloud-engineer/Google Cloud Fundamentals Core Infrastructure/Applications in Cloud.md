#### Cloud Run
- Managed compute platform for stateless containers and Pub/Sub events.
- Serverless: No infrastructure management required (focus on code).
- The Cloud Run developer workflow is a straightforward three-step process.
	- First, you write your application using your favorite programming language.
	- This application should start a server that listens for web requests.
	- Second, you build and package your application into a container image.
	- Third, the container image is pushed to Artifact Registry, where Cloud Run will deploy it.
- Once you’ve deployed your container image, you’ll get a unique HTTPS URL back.
- Cloud Run then starts your container on demand to handle requests, and ensures that all incoming requests are handled by dynamically adding and removing containers.