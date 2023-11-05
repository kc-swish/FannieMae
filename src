#include <fstream>
#include <iostream>
#include <sstream>
#include <string>
using namespace std;
const int MAX_RECORDS = 10; // Adjust this as needed

struct Record {
  int ID;
  double GrossMonthlyIncome;
  double CreditCardPayment;
  double CarPayment;
  double StudentLoanPayments;
  double AppraisedValue;
  double DownPayment;
  double LoanAmount;
  double MonthlyMortgagePayment;
  int CreditScore;
};
// Function prototype
bool isFrontEndDTIAcceptable(double housingExpenses, double grossMonthlyIncome);
bool isLTVAcceptable(double appraisedValue, double loanAmount,
                     double downPayment);
bool isDTIAcceptable(double grossMonthlyIncome, double dtiThreshold,
                     double monthlyDebt);
double monthlyDebt(double carPayment, double creditCardPayment,
                   double studentLoanPayments, double monthlyMortgagePayment);
bool isFrontEndDTIAcceptable(double monthlyMortgagePayment,
                             double grossMonthlyIncome);
bool creditRating(int creditScore);

int main() {
  string filename = "HackUTD-2023-HomeBuyerInfo.csv";
  ifstream file(filename);

  if (!file.is_open()) {
    cout << "Failed to open the file." << endl;
    return 1;
  }

  string line;
  Record records;         // Store the data in a single record
  double totalDebt = 0.0; // Initialize total debt
  int recordCount = 0;    // Initialize the record count

  // Read and discard the header line
  getline(file, line);

  while (getline(file, line) && recordCount < MAX_RECORDS) {
    stringstream ss(line);

    char comma;
    ss >> records.ID >> comma >> records.GrossMonthlyIncome >> comma >>
        records.CreditCardPayment >> comma >> records.CarPayment >> comma >>
        records.StudentLoanPayments >> comma >> records.AppraisedValue >>
        comma >> records.DownPayment >> comma >> records.LoanAmount >> comma >>
        records.MonthlyMortgagePayment >> comma >> records.CreditScore;

    // Calculate the monthly debt for this record
    double carPayment = records.CarPayment;
    double creditCardPayment = records.CreditCardPayment;
    double studentLoanPayments = records.StudentLoanPayments;
    double monthlyMortgagePayment = records.MonthlyMortgagePayment;

    double recordDebt =
        monthlyDebt(carPayment, creditCardPayment, studentLoanPayments,
                    monthlyMortgagePayment);
    // LTV calculation
    double appraisedValue = records.AppraisedValue;
    double loanAmount = records.LoanAmount;
    double downPayment = records.DownPayment; // Calculate the down payment

    bool isAcceptableLTV =
        isLTVAcceptable(appraisedValue, loanAmount, downPayment);

    if (isAcceptableLTV) {
      cout << "LTV Ratio is Acceptable" << endl;
    } else {
      cout << "LTV Ratio is Not Acceptable" << endl;
    }

    // Use the actual gross monthly income from the dataset
    double grossMonthlyIncome = records.GrossMonthlyIncome;

    // Check credit rating
    bool eligible = creditRating(records.CreditScore);

    // Add this record's debt to the total debt
    totalDebt += recordDebt;

    cout << "ID: " << records.ID << " Total Monthly Debt: " << recordDebt
         << " Credit Score: " << records.CreditScore
         << " Credit Rating: " << (eligible ? "Eligible" : "Not Eligible")
         << endl;

    // Check if the DTI ratio is acceptable
    double dtiThreshold = 36.0; // Example DTI threshold
    bool isAcceptableDTI =
        isDTIAcceptable(grossMonthlyIncome, dtiThreshold, recordDebt);

    if (isAcceptableDTI) {
      cout << "DTI Ratio is Acceptable" << endl;
    } else {
      cout << "DTI Ratio is Not Acceptable" << endl;
    }
    bool isAcceptable =
        isFrontEndDTIAcceptable(monthlyMortgagePayment, grossMonthlyIncome);

    if (isAcceptable) {
      cout << "Front-end DTI Ratio is Acceptable" << endl;
    } else {
      cout << "Front-end DTI Ratio is Not Acceptable" << endl;
    }

    recordCount++;
    cout << endl;
  }
  // Example gross monthly income

  // Print the total debt
  cout << "Total Monthly Debt for all records: " << totalDebt << endl;

  file.close();
  return 0;
}

double monthlyDebt(double carPayment, double creditCardPayment,
                   double studentLoanPayments, double monthlyMortgagePayment) {
  return carPayment + creditCardPayment + studentLoanPayments +
         monthlyMortgagePayment;
}

bool creditRating(int creditScore) {
  // Check if the credit score is greater than or equal to 640
  return creditScore >= 640;
}

bool isLTVAcceptable(double appraisedValue, double loanAmount,
                     double downPayment) {
  if (downPayment < 0.0 || appraisedValue <= 0.0) {
    // Handle cases where downPayment is negative or appraisedValue is not valid
    cout << "Invalid downPayment or appraisedValue." << endl;
    return false; // Indicate that the LTV is not acceptable
  }

  // Calculate the LTV ratio
  double ltvRatio = (loanAmount / appraisedValue) * 100.0;

  // Calculate the additional monthly PMI cost (1% of the house value per year)
  double pmiCost = (appraisedValue * 0.01) / 12.0;

  // Check if the LTV ratio is less than 80%
  if (ltvRatio < 80.0) {
    // LTV is acceptable, and no PMI is required
    cout << "LTV Ratio is Acceptable" << endl;
    return true;
  } else {
    // LTV is not acceptable, and PMI is required
    cout << "LTV Ratio is Not Acceptable" << endl;
    cout << "PMI Cost: $" << pmiCost << " per month" << endl;
    return false;
  }
}

bool isDTIAcceptable(double grossMonthlyIncome, double dtiThreshold,
                     double monthlyDebt) {
  if (grossMonthlyIncome <= 0.0 || dtiThreshold <= 0.0) {
    // Handle cases where grossMonthlyIncome or dtiThreshold is not valid
    cout << "Invalid grossMonthlyIncome or dtiThreshold." << endl;
    return false; // Indicate that the DTI is not acceptable
  }

  // Calculate the DTI ratio
  double dtiRatio = (monthlyDebt / grossMonthlyIncome) * 100.0;

  // Check if the DTI ratio is less than or equal to the specified threshold
  return dtiRatio <= dtiThreshold;
}
bool isFrontEndDTIAcceptable(double monthlyMortgagePayment,
                             double grossMonthlyIncome) {
  if (grossMonthlyIncome <= 0.0) {
    // Handle cases where grossMonthlyIncome is not valid
    cout << "Invalid grossMonthlyIncome." << endl;
    return false; // Indicate that the DTI is not acceptable
  }

  // Calculate the Front-end DTI ratio
  double frontEndDTIRatio =
      (monthlyMortgagePayment / grossMonthlyIncome) * 100.0;

  // Check if the Front-end DTI ratio is less than or equal to 28%
  if (frontEndDTIRatio <= 28.0) {
    return true; // The DTI is acceptable
  } else {
    return false; // The DTI is not acceptable
  }
}
