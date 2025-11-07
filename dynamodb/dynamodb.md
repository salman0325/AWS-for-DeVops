
# 🚀 AWS DynamoDB + Lambda Tutorial

This guide explains what **DynamoDB** is, how to **create a DynamoDB table**, and how to **insert data into it using AWS Lambda**.

---

## 🧠 What is DynamoDB?

**Amazon DynamoDB** is a **NoSQL database service** provided by **AWS**.  
It is **fully managed**, **serverless**, and **highly scalable** — perfect for applications that need fast and consistent data access.

### ⚙️ Key Features of DynamoDB:
1. **Serverless** – No need to manage servers or infrastructure.
2. **Scalable** – Automatically scales up or down to handle traffic.
3. **High Performance** – Millisecond response time for reads/writes.
4. **Flexible Data Model** – Store data as key-value or document-based.
5. **Global Tables** – Replicate data across multiple regions.
6. **Built-in Security** – Integrates with AWS IAM for access control.
7. **Streams** – Capture real-time changes in data.
8. **Integration with Lambda** – Enables event-driven architectures.

---

## 🛠️ Step 1: Create a DynamoDB Table

1. Go to **AWS Management Console → DynamoDB**.
2. Click **“Create table”**.
3. Fill the details:
   - **Table name:** `UsersTable`
   - **Partition key:** `userId` (String)
4. Leave other settings as default and click **“Create”**.

---

## ⚡ Step 2: Create a Lambda Function

1. Go to **AWS Lambda → Create Function**.
2. Choose:
   - **Author from scratch**
   - **Function name:** `AddUserFunction`
   - **Runtime:** Node.js 18.x (or Python 3.10)
3. Click **Create function**.

---

## 🧩 Step 3: Give Lambda Access to DynamoDB

1. Go to **Configuration → Permissions**.
2. Under **Execution role**, click the role name.
3. Attach this policy to allow DynamoDB access:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:PutItem"
      ],
      "Resource": "arn:aws:dynamodb:*:*:table/UsersTable"
    }
  ]
}
````

---

## 💻 Step 4: Add Code to Insert Data (Example in Node.js)

```javascript
import { DynamoDBClient, PutItemCommand } from "@aws-sdk/client-dynamodb";

const client = new DynamoDBClient({ region: "us-east-1" });

export const handler = async (event) => {
  const { userId, name, email } = JSON.parse(event.body);

  const params = {
    TableName: "UsersTable",
    Item: {
      userId: { S: userId },
      name: { S: name },
      email: { S: email }
    }
  };

  try {
    await client.send(new PutItemCommand(params));
    return {
      statusCode: 200,
      body: JSON.stringify({ message: "User added successfully!" })
    };
  } catch (error) {
    return {
      statusCode: 500,
      body: JSON.stringify({ error: error.message })
    };
  }
};
```

---

## 📡 Step 5: Test Your Lambda

In the **Test tab**:

```json
{
  "body": "{\"userId\": \"101\", \"name\": \"John Doe\", \"email\": \"john@example.com\"}"
}
```

Run the test → You should see:
✅ “User added successfully!”

---

## 🧾 Example DynamoDB Record

| userId | name     | email                                       |
| ------ | -------- | ------------------------------------------- |
| 101    | John Doe | [john@example.com](mailto:john@example.com) |

---

## 🧠 Summary

| Component       | Description                                             |
| --------------- | ------------------------------------------------------- |
| **DynamoDB**    | NoSQL database for fast, scalable data storage          |
| **Lambda**      | Serverless compute that runs code in response to events |
| **Integration** | Lambda function can insert or read data from DynamoDB   |
| **Use Case**    | Building serverless APIs, IoT apps, chat systems, etc.  |

---

**Author:** salman khan


```

