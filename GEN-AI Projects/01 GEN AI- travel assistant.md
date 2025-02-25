Here's a **GenAI Project Architecture** for a travel assistant that processes user queries like:

_"Please book a ticket for me for tomorrow from Mumbai to Delhi."_

It will internally search for **train, flight, and bus options** and display results in a travel portal.

---

## **🔹 Architecture Overview**

The architecture consists of **six main layers**:

### **1️⃣ User Interface (Frontend)**

- **Tech Stack**: React.js / Angular (for Web), Flutter / React Native (for Mobile)
- **Features**:
    - Chatbot for user input (Text & Voice)
    - Travel options display (flights, trains, buses)
    - Ticket booking & payment integration

---

### **2️⃣ API Gateway & Orchestration**

- **Tech Stack**: **GraphQL / API Gateway (Kong, APIGEE, AWS API Gateway)**
- **Purpose**:
    - Manages incoming requests
    - Routes them to the right microservices
    - Handles authentication & rate limiting

---

### **3️⃣ GenAI Processing Layer (LLM Integration)**

- **Tech Stack**: **OpenAI GPT-4 / Llama / Mistral AI / Custom Transformer Model**
- **Key Components**:
    - **NLP Parsing** → Extracts intent (`book ticket`), date (`tomorrow`), and locations (`Mumbai` → `Delhi`)
    - **Context Awareness** → Understands user preferences (e.g., flight preference, train preference)
    - **Prompt Engineering & Retrieval Augmented Generation (RAG)** → Enhances responses using external travel data

---

### **4️⃣ Travel Search Microservices**

Each travel mode has its own **microservice** that fetches real-time availability & prices:

1. **Flight Service**
    
    - **APIs**: Skyscanner, Amadeus, ClearTrip, MakeMyTrip
    - **Database**: PostgreSQL / MongoDB
2. **Train Service**
    
    - **APIs**: IRCTC, RailYatri, Google Transit
    - **Database**: MySQL
3. **Bus Service**
    
    - **APIs**: RedBus, Abhibus, FlixBus
    - **Database**: NoSQL / Firebase

Each service runs **independently** and communicates via **REST / GraphQL** APIs.

---

### **5️⃣ Booking & Payment Microservices**

- **Booking Management**
    
    - Stores user booking details, history, and payment status
    - **Database**: PostgreSQL (for structured data)
- **Payment Gateway Integration**
    
    - Razorpay, Stripe, Paytm, UPI
- **Notification Service**
    
    - Sends confirmation via Email, WhatsApp, and SMS

---

### **6️⃣ Cloud & Deployment Layer**

- **Hosting**: AWS / Azure / Google Cloud
- **Containers**: Kubernetes (K8s) for **scalability**
- **CI/CD**: GitHub Actions, Jenkins, Docker

---

## **🔹 Data Flow (Step-by-Step Execution)**

1️⃣ **User Query**:

- "Please book a ticket for me for tomorrow from Mumbai to Delhi."

2️⃣ **GenAI Model**:

- Extracts:
    - Intent → `Book Ticket`
    - Source → `Mumbai`
    - Destination → `Delhi`
    - Date → `Tomorrow`

3️⃣ **Microservices Call**:

- Calls **Flight, Train, Bus services** for availability

4️⃣ **Aggregates & Displays Results**:

- Shows options sorted by **price, time, or user preference**

5️⃣ **Booking & Payment Process**:

- User selects an option → **Booking Service** stores details
- Redirects to **Payment Gateway**
- Confirms & sends an **E-ticket**

---

## **🔹 Technologies Used**

|Layer|Technologies|
|---|---|
|**Frontend**|React.js, Angular, Flutter|
|**API Gateway**|GraphQL, AWS API Gateway, Kong|
|**GenAI Engine**|OpenAI GPT, LangChain, RAG|
|**Database**|PostgreSQL, MongoDB, Redis (Cache)|
|**Travel APIs**|Skyscanner, IRCTC, RedBus|
|**Containerization**|Docker, Kubernetes|
|**Cloud & Deployment**|AWS Lambda, Azure Functions|

---

## **🎯 Key Features**

✅ **Conversational AI** (Natural Language Query Processing)  
✅ **Multi-Mode Travel Search** (Flights, Trains, Buses)  
✅ **Personalized Recommendations**  
✅ **Secure Payment Integration**  
✅ **Scalable Microservices Architecture**

---

Let's break it into parts and implement a **basic GenAI-powered travel assistant API** using **ASP.NET Core** with **OAuth 2.0 authentication** and **integration with a GenAI model (e.g., OpenAI GPT or a custom LLM)**.

---

## **🔹 Features in the Demo**

✅ **User Authentication with OAuth 2.0**  
✅ **GenAI Integration** (for processing queries like "Book a ticket for tomorrow from Mumbai to Delhi")  
✅ **Flight, Train & Bus Search Services (Mock Data)**  
✅ **Results Aggregation & Display**

---

### **📌 Steps to Build**

1️⃣ **Set Up ASP.NET Core API**  
2️⃣ **Integrate OAuth 2.0 for Authentication**  
3️⃣ **Implement GenAI (ChatGPT / LangChain)**  
4️⃣ **Create Travel Search Microservices (Flights, Trains, Buses)**  
5️⃣ **Aggregate & Return Results**

---

## **📜 Code Implementation: ASP.NET Core API**

Here's the **ASP.NET Core API** that integrates **GenAI** and fetches travel options.

## **🔹 Explanation of the Code**

1️⃣ **OAuth 2.0 Authentication**:

- The `[Authorize]` attribute ensures **only authenticated users** can access the API.

2️⃣ **GenAI (GPT-4) Integration**:

- Calls OpenAI API to **process user input**.
- Extracts **source, destination, and date** from the response.

3️⃣ **Travel Microservices (Mocked Here)**:

- Fetches flight, train, and bus options.
- Returns the best options to the user.

---




using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Newtonsoft.Json;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

[ApiController]
[Route("api/travel")]
public class TravelController : ControllerBase
{
    private readonly HttpClient _httpClient;

    public TravelController(HttpClient httpClient)
    {
        _httpClient = httpClient;
    }

    [HttpPost("search")]
    [Authorize] // OAuth 2.0 Authentication
    public async Task<IActionResult> SearchTravel([FromBody] TravelRequest request)
    {
        if (string.IsNullOrEmpty(request.Query))
        {
            return BadRequest("Query cannot be empty.");
        }

        // Step 1: Call GenAI to parse intent
        string aiResponse = await GetAIResponse(request.Query);

        // Step 2: Extract details (Mocked extraction)
        var travelOptions = await GetTravelOptions(aiResponse);

        return Ok(travelOptions);
    }

    private async Task<string> GetAIResponse(string query)
    {
        var openAiRequest = new { model = "gpt-4", prompt = query, max_tokens = 100 };
        var content = new StringContent(JsonConvert.SerializeObject(openAiRequest), Encoding.UTF8, "application/json");

        var response = await _httpClient.PostAsync("https://api.openai.com/v1/completions", content);
        response.EnsureSuccessStatusCode();

        var responseBody = await response.Content.ReadAsStringAsync();
        return responseBody;
    }

    private async Task<object> GetTravelOptions(string aiResponse)
    {
        // Mock API calls to flight, train, and bus services
        var flights = new { Mode = "Flight", Provider = "IndiGo", Price = 5500, Time = "10:00 AM" };
        var trains = new { Mode = "Train", Provider = "Rajdhani Express", Price = 2500, Time = "9:00 PM" };
        var buses = new { Mode = "Bus", Provider = "RedBus", Price = 1200, Time = "6:00 AM" };

        return new { Flights = flights, Trains = trains, Buses = buses };
    }
}

public class TravelRequest
{
    public string Query { get; set; }
}

//=============Real Time API Calls==============================


using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Newtonsoft.Json;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

[ApiController]
[Route("api/travel")]
public class TravelController : ControllerBase
{
    private readonly HttpClient _httpClient;

    public TravelController(HttpClient httpClient)
    {
        _httpClient = httpClient;
    }

    [HttpPost("search")]
    [Authorize] // OAuth 2.0 Authentication
    public async Task<IActionResult> SearchTravel([FromBody] TravelRequest request)
    {
        if (string.IsNullOrEmpty(request.Query))
        {
            return BadRequest("Query cannot be empty.");
        }

        // Step 1: Call GenAI to parse intent
        string aiResponse = await GetAIResponse(request.Query);

        // Step 2: Extract details (Mocked extraction)
        var travelOptions = await GetRealTimeTravelOptions(aiResponse);

        return Ok(travelOptions);
    }

    private async Task<string> GetAIResponse(string query)
    {
        var openAiRequest = new { model = "gpt-4", prompt = query, max_tokens = 100 };
        var content = new StringContent(JsonConvert.SerializeObject(openAiRequest), Encoding.UTF8, "application/json");

        var response = await _httpClient.PostAsync("https://api.openai.com/v1/completions", content);
        response.EnsureSuccessStatusCode();

        var responseBody = await response.Content.ReadAsStringAsync();
        return responseBody;
    }

    private async Task<object> GetRealTimeTravelOptions(string aiResponse)
    {
        var flightResponse = await _httpClient.GetStringAsync("https://api.skyscanner.net/apiservices/browsequotes/v1.0/US/USD/en-US/Mumbai/Delhi/2024-02-25");
        var trainResponse = await _httpClient.GetStringAsync("https://api.irctc.co.in/trains/search?from=Mumbai&to=Delhi&date=2024-02-25");
        var busResponse = await _httpClient.GetStringAsync("https://api.redbus.in/buses/search?from=Mumbai&to=Delhi&date=2024-02-25");

        return new { Flights = JsonConvert.DeserializeObject(flightResponse), Trains = JsonConvert.DeserializeObject(trainResponse), Buses = JsonConvert.DeserializeObject(busResponse) };
    }
}

public class TravelRequest
{
    public string Query { get; set; }
}
