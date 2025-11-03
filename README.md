# E-Commerce Order Processing Workflow

[Watch 2-Minute Demo](#)  
*Video walkthrough coming soon — showing workflow canvas and execution results*

Developer demo showing how to build a production-ready e-commerce workflow in Orkes Conductor.  
The workflow automates order creation, payment processing, inventory updates, and fulfillment confirmation.

---

## Prerequisites

- **Google account or email** to sign into [Orkes Playground](https://play.orkes.io) (free)
- Basic understanding of REST APIs
- Familiarity with JSON (helpful but not required)

---

## The Problem We're Solving

When a customer places an order on an e-commerce site, multiple things need to happen:
1. Process their payment  
2. Check if the payment succeeded  
3. If yes: reserve inventory, send confirmation email, notify warehouse  
4. If no: send failure notification  

Coordinating all these steps across different services is complex. Conductor makes it simple.

---

## Step 1: Access Orkes Playground

Go to [Orkes Playground](https://play.orkes.io).

You'll see the Launch Pad with two options: browse pre-built workflow templates or start from scratch.

<div align="center">
  <img src="./images/Orkes_dashboard.png" width="80%" alt="Orkes Launch Pad">
  <p><em>The Orkes Conductor Launch Pad — click "Start from Scratch" to begin.</em></p>
</div>

For this tutorial, click **"Start from Scratch"** so you can learn how to build a workflow from the ground up.

You'll be prompted to log in.

<div align="center">
  <img src="./images/Login_Screen_edit.png" width="40%" alt="Login Prompt">
  <p><em>Login screen — sign in with Google or email to access the platform.</em></p>
</div>

Sign in with your Google account or email. Once logged in, you'll be taken to the main Conductor interface.

---

## Step 2: Navigate to Workflow Definitions

<div align="center">
  <img src="./images/Now_Workflow_Canvas.png" width="80%" alt="New Workflow Canvas">
  <p><em>The workflow creation interface after clicking "Start from Scratch" — shows an empty canvas ready to build.</em></p>
</div>

From the main interface, navigate to **Definitions → Workflow** in the left sidebar.  
Click the **"Create Workflow"** button in the top right.

---

## Step 3: Set Up Workflow Details

You'll see the workflow configuration form. Fill it out:

**Name:** `E-Commerce Order Processing`

**Description:**  
When a customer places an order, this workflow handles everything: payment processing, inventory checks, email confirmations, and shipping coordination.  
Shows how to build resilient, production-ready automation for e-commerce systems.

Leave the other settings at their defaults for now.

<div align="center">
  <img src="./images/Workflow_Details_Filled.png" width="40%" alt="Workflow Details Filled">
  <p><em>Workflow Details form filled with name and description.</em></p>
</div>

---

## Step 4: Define Input Parameters

Scroll down to **Input and Output Parameters**.  
Click **"Add parameter"** five times and add these keys:

1. `orderId`  
2. `customerId`  
3. `customerEmail`  
4. `totalAmount`  
5. `items`  

<div align="center">
  <img src="./images/Input_Ouput_Parameters.png" width="40%" alt="Input and Output Parameters">
  <p><em>All input parameters (orderId, customerId, customerEmail, totalAmount, items) and output parameter (trackingNumber) configured.</em></p>
</div>

---

## Step 5: Add an Output Parameter

Still in the parameters section, scroll to **Output parameters** and add:  
**Parameter:** `trackingNumber`

Leave the Value field blank — it will be filled during execution.

---

## Step 6: Build the Payment Processing Task

Click the **"Task"** tab at the top.

<div align="center">
  <img src="./images/Task_Tab_Empty_Canvas.png" width="80%" alt="Task Tab Empty Canvas">
  <p><em>The Task tab shows a visual canvas with Start and End nodes.</em></p>
</div>

Click the **+ button** below Start.

<div align="center">
  <img src="./images/Task_Picker_Menu.png" width="60%" alt="Task Picker Menu">
  <p><em>The task picker menu — select HTTP Task.</em></p>
</div>

Select **"HTTP Task"** from Quick Add.

---

## Step 7: Configure the Payment Task

Click on the HTTP task to configure it.

**Reference name:** `process_payment`  
**Method:** `POST`  
**URL:** `https://httpbin.org/post`  

Scroll to the **Body** section and replace `{}` with:
```json
{
  "orderId": "${workflow.input.orderId}",
  "customerId": "${workflow.input.customerId}",
  "totalAmount": "${workflow.input.totalAmount}",
  "paymentMethod": "credit_card"
}
```

<div align="center">
  <img src="./images/HTTP_Task_Configuration.png" width="70%" alt="HTTP Task Configuration">
  <p><em>HTTP task configured with POST method and order details.</em></p>
</div>

---

## Step 8: Add Conditional Logic

Click the **+ button** below `process_payment`.

<div align="center">
  <img src="./images/Add_Switch_Task.png" width="80%" alt="Add Switch Task">
  <p><em>Add a Switch task for conditional routing.</em></p>
</div>

Select **"Switch"** task.

<div align="center">
  <img src="./images/Switch_Task_Added.png" width="80%" alt="Switch Task Added">
  <p><em>The Switch task appears on the canvas as a diamond shape for branching.</em></p>
</div>

---

## Step 9: Configure the Payment Check

Click on the Switch task.

**Reference name:** `check_payment_status`  
**Key:** `paid` → **Value:** `success`  
**Evaluate:** `ECMASCRIPT`  
**Code:**
```javascript
$.paid
```

**Switch case key:** `success`

<div align="center">
  <img src="./images/Switch-task-configuration.png" width="70%" alt="Switch Task Configuration">
  <p><em>Switch task configured with ECMASCRIPT evaluation.</em></p>
</div>

---

## Step 10: Save and Test Your Workflow

Click **"Save"**.

<div align="center">
  <img src="./images/Save_Workflow.png" width="80%" alt="Save Workflow">
  <p><em>Click Save to validate and store your workflow configuration.</em></p>
</div>

Run your workflow to test. You'll see tasks executing in real-time.

<div align="center">
  <img src="./images/Execution_screeen.png" width="80%" alt="Workflow Execution">
  <p><em>Completed workflow execution showing payment processing and confirmation.</em></p>
</div>

---

## Step 11: Add Email Confirmation Task

On the success branch, click the **+ icon** and add another **HTTP Task**.

**Reference name:** `send_confirmation_email`  
**Method:** `POST`  
**URL:** `https://httpbin.org/post`

**Body:**
```json
{
  "to": "${workflow.input.customerEmail}",
  "subject": "Order Confirmation",
  "orderId": "${workflow.input.orderId}",
  "message": "Your order has been confirmed!"
}
```

<div align="center">
  <img src="./images/Email_Task_Configuration.png" width="88%" alt="Email Task Configuration">
  <p><em>Email confirmation task sends notification when payment succeeds.</em></p>
</div>

Save your workflow — it should look like this:

<div align="center">
  <img src="./images/Complete_Workflow_Structure.png" width="80%" alt="Complete Workflow Structure">
  <p><em>Complete workflow showing all tasks connected: payment → switch → email.</em></p>
</div>

---

## Workflow Summary
```
Start
  ↓
Process Payment (HTTP POST)
  ↓
Check Payment Status (SWITCH)
  ↓ (success)
Send Confirmation Email (HTTP POST)
  ↓
End
```

---

## Next Steps

- Add **FORK Tasks** to run inventory updates and notifications in parallel.
- Add error handling for payment failures.
- Connect to real APIs (Stripe, PayPal, SendGrid).
- Configure retry logic and timeouts for resilience.

---

## Resources

- [Orkes Documentation](https://orkes.io/content/)
- [HTTP Task Reference](https://orkes.io/content/reference-docs/operators/http-task)
- [Switch Task Reference](https://orkes.io/content/reference-docs/operators/switch-task)
- [Conductor OSS GitHub](https://github.com/conductor-oss/conductor)

---

**Questions or suggestions?**  
Open an issue or reach out!
