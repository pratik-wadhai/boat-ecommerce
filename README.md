
Modern Full Stack ECommerce Application with Stripe & Sanity
![eCommerce](https://user-images.githubusercontent.com/70088342/160780701-7bb38a57-76bd-49a2-a4ec-49f89c50a7c7.png)

## Boat E-commerce

Boat E-commerce is a full-stack web application for an online store that sells boats headphones. It is built using the Nextjs React framework and uses HTML, CSS, and JavaScript for the front-end. The application includes features for user authentication, browsing boat listings, adding items to a shopping cart, and processing payments through Stripe.

## Installation
To install and run the application on your local machine, you will need to have Python 3.x and pip installed. Then, follow these steps:

1. Clone the repository:

```
git clone https://github.com/pratik-wadhai/boat-ecommerce.git
```

2. Navigate to the project directory:
  
  ```
  cd boat-ecommerce
   ```
   
3. Install the required dependencies using npm :
 ```
 npm install --legacy-peer-deps
 ```
 4. Create a .env file in the project root directory with the following content:
 ```
 NEXT_PUBLIC_SANITY_TOKEN=<your_secret_key>
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=<your_stripe_publishable_key>
NEXT_PUBLIC_STRIPE_SECRET_KEY=<your_stripe_secret_key>
```
5. Create sanity project using these commands:
  a. create sanity project and give it a name 
  ```
  npm create sanity@latest -- --template clean --create-project "Sanity Project" --dataset production
  ```
  b. cd project folder:
  ```
  cd sanity
  ```
  c. start your sanity project using , you can add or delete product and banner:
  ```
  sanity start
  ```
  or 
  ```
  npm run dev
  ```
  d. go to sanity manage and copy the sanity project id:
  ```
  sanity manage
  ```
  e. for more information about sanity commands :
      ```
      sanity help
      ```
 6. Insert your stripe shipping code and your sanity project id in stripe.js:
```
import Stripe from "stripe";

const stripe = new Stripe(process.env.NEXT_PUBLIC_STRIPE_SECRET_KEY);

export default async function handler(req, res) {
  if (req.method === "POST") {
    try {
      const params = {
        submit_type: "pay",
        mode: "payment",
        payment_method_types: ["card"],
        billing_address_collection: "auto",
        shipping_options: [
          { shipping_rate: "your shipping code" },
          { shipping_rate: "your shipping code" },
        ],
        line_items: req.body.map((item) => {
          const img = item.image[0].asset._ref;
          const newImage = img
            .replace(
              "image-",
              "https://cdn.sanity.io/images/<sanity project id>/production/"
            )
            .replace("-webp", ".webp");

          return {
            price_data: {
              currency: "inr",
              product_data: {
                name: item.name,
                images: [newImage],
              },
              unit_amount: item.price * 100,
            },
            adjustable_quantity: {
              enabled: true,
              minimum: 1,
            },
            quantity: item.quantity,
          };
        }),
        success_url: `${req.headers.origin}/success`,
        cancel_url: `${req.headers.origin}/canceled`,
      };

      // Create Checkout Sessions from body params.
      const session = await stripe.checkout.sessions.create(params);

      res.status(200).json(session);
    } catch (err) {
      res.status(err.statusCode || 500).json(err.message);
    }
  } else {
    res.setHeader("Allow", "POST");
    res.status(405).end("Method Not Allowed");
  }
}

```

7. Also change sanity code in client.js in lib directory:
```
import sanityClient from "@sanity/client";
import imageUrlBuilder from "@sanity/image-url";

export const client = sanityClient({
  projectId: "sanity project id",
  dataset: "production",
  apiVersion: "2023-02-20",
  useCdn: true,
  token: process.env.NEXT_PUBLIC_SANITY_TOKEN,
});

const builder = imageUrlBuilder(client);

export const urlFor = (source) => builder.image(source);

```
8. Start the development server:
```
npm run dev
```


![image](https://user-images.githubusercontent.com/70088342/160780701-7bb38a57-76bd-49a2-a4ec-49f89c50a7c7.png)
![image](https://user-images.githubusercontent.com/70088342/160780206-9cfe7c0a-3d8e-4a20-a055-b12efebe6c30.png)
![image](https://user-images.githubusercontent.com/70088342/160780265-692d37ac-7209-4d53-957a-e94b37d123c0.png)
![image](https://user-images.githubusercontent.com/70088342/160780381-7c947640-422e-4729-abae-21911e9bc716.png)
![image](https://user-images.githubusercontent.com/70088342/160780549-111ed048-cd4b-4740-b2fd-2c6fc3520c52.png)
![image](https://user-images.githubusercontent.com/70088342/160780884-22d6025e-9b7d-4493-8136-b3dfbf00a32f.png)


## Usage

Once the application is running, you can use it to browse boat listings, add items to your shopping cart, and process payments using the Stripe payment gateway. You can also create an account and log in to access additional features, such as viewing your order history and managing your saved boats.

## Contributing
If you would like to contribute to the development of Boat E-commerce, please open an issue or pull request on GitHub. Any contributions are welcome and appreciated!
   
