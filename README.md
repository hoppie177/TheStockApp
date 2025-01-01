# TheStockApp











All In One Stocks App (Not Official Name)
(December 2024)

Idea brought to you by Savvi Media (Stenoree Allen)

Designed and Created by Savvi Media











Produced in Charleston IL, December of 2024


The Problem:

There are multiple stock, ETF, and trading apps out there. These sites and applications all have their own platforms with their own logins for the users. Having money across multiple apps can be confusing and or overwhelming for any user. Especially when it comes to managing your portfolio and actively trading. 

Our Solution:

We plan on creating an application that can take all of these trading platforms, and bring them into one place. Allowing users to see their full portfolio, trade on all of their individual accounts from one spot, see real time charts, and more. This will make it easier for users to not only see their full portfolio in one place but will also allow them to manage it, hands on. Being able to buy, sell, and maybe even transfer their funds via our application. 

How Will It Work?

The plan is to code the website using either the Flutter framework (Allowing us to launch across multiple operating systems) or just start natively with the iOS framework Xcode and Swift. Once we decide the framework and language we will begin by focusing on the APIs from each company. We’ll also setup Firebase or AWS for backend services like authentication, push notifications, and analytics. We plan on starting with Robinhood, then moving onto adding Coinbase, and from there add as many as we can one by one. Robinhood, Coinbase, and even TD Ameritrade all have a great source of APIs and documentation. Utilizing these APIs in our code to let users interact with all their accounts. Robinhood has API’s that allows users to see their account balance, trade, and more. We’ll inject this code within our framework along with an easy to read Ui. 

Key Functionalities:
User Login with RobinHood Credentials:
Use api_login(username, password, mfa_code) for authentication.
Store the returned RobinHood object for API requests.
Account Balance and Portfolio Summary:
Use get_portfolios(RH) to fetch portfolio summaries.
Parse and display the portfolio value, percentage, and point changes.
Live Charts and Real-time Quotes:
Use get_historicals(RH, symbol, interval, span) for historical data.
Use get_quote(RH, symbol) for real-time price updates.
Real-time Updates:
Utilize periodic polling or websockets (if supported by RobinHood API) to fetch live updates.
Suggested Tech Stack:
Frontend: Flutter with flutter_bloc for state management.
Backend: Node.js (or another language) as a proxy server for handling API calls securely.
Data Visualization: Use packages like fl_chart for live charts.


Potential Features: 

 Main portfolio that allows users to see all their account balances wrapped into one. (Provide real-time updates on portfolios, transactions, and market trends.)
Separate buttons that each represent one of their accounts (Ex. Robinhood)
Allow users to trade from each one of their accounts 
Allow users to see each one of their portfolios separately
Market news and analytics
Customizable alerts (price changes, trade execution, etc.)
Educational tools (e.g., tutorials, investment strategies).
Performance charts and financial reports.







________________________________________________________________



App Design
App Flow (Rough Draft)

#Note: Trying to focus on utilizing the main page. I believe that the main uses of this app will happen on the main page. The main goal is to create an app with Accessibility, Minimalist yet Informative Dashboards, and Easy Navigation.




Key Screens (Pages):

Dashboard: Overview of portfolios.
Account Integration: Add/manage trading accounts.
Analytics: Charts, trends, and reports.
Alerts: Notification settings.

Potential Names:

Investment Sync
The Trade Bridge 
Town Square
StockUp
Omni Invest
OmniNetwork
TradeCloud
Unity Markets
Market Unity
ConnectOmni
ConnectInvest
MarketHub
MoneyMatrix
TradeSphere

Potential Color Palette: 
(Can be changed)















App Layout (Rough Draft/ Wireframe)







Potential Risk and Issues

Users could feel that we are liable for their trading and financial decisions (Could lead to legal issues)
Because we are retrieving data from third parties, users might find us untrustworthy of their information and or we might slip and and not follow good privacy policy rules and regulations 
The app could malfunction and potentially cause false trades, or miss a trade affecting the users balance and portfolio 
The API’s could go down without anything for us to do about it, leaving our app useless until the issues resolved by our third party providers 
If we decide to gather any user information within our own database rather it be a name or birthday. We could see potential push back by government agencies and regulators. Making sure we are following the rules and guidelines when it comes to collecting user data. 
Some of the third party platforms might feel as if we are stealing their ideas, and or traffic and will cut access to their API’s off from us. Might even try to get the app shutdown all together if they feel as if we might become competition. (But in reality we don’t work without them, so we can’t become competition, only an ally).


A Good Idea of how the Code will Look and Integrate with the APIs 

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'package:fl_chart/fl_chart.dart';
import 'dart:convert';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'RobinHood Portfolio',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: LoginScreen(),
    );
  }
}

class LoginScreen extends StatelessWidget {
  final TextEditingController _usernameController = TextEditingController();
  final TextEditingController _passwordController = TextEditingController();

  Future<void> login(BuildContext context, String username, String password) async {
    final response = await http.post(
      Uri.parse('https://your-backend-api/login'), // Replace with your backend endpoint
      body: jsonEncode({'username': username, 'password': password}),
      headers: {'Content-Type': 'application/json'},
    );

    if (response.statusCode == 200) {
      final portfolioValue = jsonDecode(response.body)['portfolioValue'];
      final dataPoints = (jsonDecode(response.body)['historicalData'] as List)
          .map((e) => FlSpot(e['time'].toDouble(), e['value'].toDouble()))
          .toList();

      Navigator.push(
        context,
        MaterialPageRoute(
          builder: (context) => PortfolioScreen(portfolioValue, dataPoints),
        ),
      );
    } else {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Login failed. Please try again.')),
      );
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Login')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _usernameController,
              decoration: InputDecoration(labelText: 'Username'),
            ),
            TextField(
              controller: _passwordController,
              decoration: InputDecoration(labelText: 'Password'),
              obscureText: true,
            ),
            SizedBox(height: 16),
            ElevatedButton(
              onPressed: () => login(context, _usernameController.text, _passwordController.text),
              child: Text('Login'),
            ),
          ],
        ),
      ),
    );
  }
}

class PortfolioScreen extends StatelessWidget {
  final String portfolioValue;
  final List<FlSpot> dataPoints;

  PortfolioScreen(this.portfolioValue, this.dataPoints);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Portfolio Summary')),
      body: Column(
        children: [
          Padding(
            padding: const EdgeInsets.all(16.0),
            child: Text(
              'Total Portfolio Value: $portfolioValue',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
          ),
          Expanded(
            child: Padding(
              padding: const EdgeInsets.all(16.0),
              child: LineChart(
                LineChartData(
                  lineBarsData: [
                    LineChartBarData(
                      spots: dataPoints,
                      isCurved: true,
                      colors: [Colors.blue],
                      barWidth: 4,
                      isStrokeCapRound: true,
                      belowBarData: BarAreaData(show: true, colors: [Colors.blue.withOpacity(0.3)]),
                    ),
                  ],
                  titlesData: FlTitlesData(
                    leftTitles: SideTitles(showTitles: true),
                    bottomTitles: SideTitles(showTitles: true),
                  ),
                ),
              ),
            ),
          ),
        ],
      ),
    );
  }
}

Explanation:
Login Functionality:
Accepts username and password.
Sends a POST request to a backend API endpoint (https://your-backend-api/login).
Portfolio Display:
Displays portfolio value received from the backend.
Renders a live chart using the fl_chart package.
Live Chart:
Uses the LineChart widget from the fl_chart package.
Dynamically plots points received from the API.
Prerequisites:
Backend API Endpoint: Replace https://your-backend-api/login with your actual backend endpoint.
Dependencies: Add the following dependencies to your pubspec.yaml file:
yaml


Yaml File:

dependencies:
  flutter:
    sdk: flutter
  http: ^0.15.0
  fl_chart: ^0.40.0

Run (flutter pub get) to install dependencies.
