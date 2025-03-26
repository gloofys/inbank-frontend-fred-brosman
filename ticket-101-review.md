# TICKET-101 Review
This review covers the frontend part of TICKET-101.
For the backend review, please refer to the corresponding file in the backend repository.



## What Was Done Well

- Clean and intuitive user interface.
- The form captures all required fields: national ID, loan amount, and loan period.
- Backend integration is functional using the `api_service.dart` class.
- Good use of reusable widgets and consistent Flutter components (e.g., `Slider.adaptive`).
- Tests for api_service and loan_form.
- API logic is separated into a dedicated class (ApiService) with proper use of dependency injection (http.Client) for testability.
- Overall project structure follows good separation of concerns and readability.


## Suggestions for improvement

- Lack of status code check before decoding response in `api_service.dart`
- Remove unnecessary instance variables (responseAmount, responsePeriod, responseError) and use local variables inside the method to keep the class stateless.
- Replace the Map<String, String> return type with a strongly-typed LoanDecision model class for improved structure, safety, and clarity.
- The form's minimum width (500px) breaks layout on smaller screens. The layout logic in LoanForm should be adjusted to handle responsiveness.
- The application currently throws a console error due to improper widget nesting.



## Fixes and Improvements

### 1. Loan Period Slider Exceeded Maximum Allowed (48 months)

According to the assignment constraints, the maximum loan period should be **48 months**, but the original slider allowed up to **60 months**.

**Fixes:**
- Slider `max` value changed from `60` to `48`.
- Slider `divisions` updated from `40` to `36` to match the range (12–48) and maintain even increments.
- Display text updated from `'60 months'` to `'48 months'`.

---

### 2. Incorrect Minimum Value Label on Loan Period Slider

The slider `min` value was correctly set to **12**, but the label text displayed `'6 months'`, which was misleading.

**Fix:**
- Updated the label to `'12 months'` to accurately reflect the slider's minimum and the rule: "Minimum loan period can be 12 months."

---

### 3. Incorrect Result Handling in `_submitForm()`

The original logic in `_submitForm()` only applied backend results if they were less than or equal to the user's input. This was a violation of the core requirement:

> _“The result should be the maximum sum which we would approve.”_

This meant that if the backend approved a higher amount or a shorter/better loan period, those values were being discarded.

**Fix:**
- Removed the conditional check in `setState()` that filtered out backend responses.
- Now, the approved amount and period from the backend are always used:
  _loanAmountResult = int.parse(result['loanAmount'].toString());
  _loanPeriodResult = int.parse(result['loanPeriod'].toString());

### 4. Incorrect use of ParentDataWidget In main.dart.
- Cannot use Expanded inside a Center. Wrap Expanded inside a Column or Row instead — or just remove the Expanded entirely.
- The app will work without this fix, but i removed the Expanded line, because the console log mess was annoying. The unnecessary Expanded was in main.dart `line 47`.

### 5. Clarified Loan Period Slider Logic
-The original slider logic for selecting loan period used:
```_loanPeriod = ((newValue.floor() / 6).round() * 6);```

While this ensures the value snaps to the nearest multiple of 6, it can be confusing — especially since the approved loan period from the backend isn't always a multiple of 6. This mismatch could lead to inconsistent expectations for the user.

Replaced it with:
```_loanPeriod = newValue.toInt();```
This makes the behavior simpler and more intuitive by directly using the slider's selected value, improving clarity and user experience.

### 6. Deprecated withOpacity in main.dart
- withOpacity is deprecated in new flutter SDK
- replaced ```selectionColor: AppColors.textColor.withOpacity(0.3),``` with ```selectionColor: AppColors.textColor.withValues(alpha: 0.3),```