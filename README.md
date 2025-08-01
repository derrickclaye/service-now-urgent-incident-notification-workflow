### 3. README.md Content

#### System Overview: Description of the working incident notification system

The working incident notification system is triggered when an incident with a priority of “Critical” is created and the Assignment Group is set to “ITSM Engineering”. Once the trigger conditions are met, the workflow sends a notification to the members of the ITSM Engineering group. The email body template for the notification includes the incident number for easy reference.

#### Implementation Steps: How you fixed the workflow and configured notifications

My investigation revealed several issues that prevented the system from functioning correctly. I resolved these issues with the following steps:

1.  **Notification Configuration:**
    * Changed the recipient group from the incorrect group to the **ITSM Engineering** group.
    * Added several users to the ITSM Engineering group and assigned the **itil** role to the group to ensure proper permissions.
    * Edited the email body template to include the incident number in the notification.

2.  **Workflow Configuration:**
    * Corrected an incorrectly formatted trigger condition.
    * Configured the workflow to trigger only when the incident's priority is set to **“Critical”** and the assignment group is set to **“ITSM Engineering”**.

Once these changes were made, the workflow and notification system began to run as intended.

#### Architecture Diagram: Visual representation of the complete system flow. Use Draw.io

<img width="456" height="599" alt="Diagram" src="https://github.com/user-attachments/assets/649249bc-e738-4f94-95ae-4da2768be73d" />


#### AI Scenario: How AI agents could enhance incident routing

Having an AI Agent who was responsible for handling critical incidents would surely enhance a company's incident notification system. If I were to create the agent for this purpose I would first start with the notification itself to ensure it includes relevant data for the agent to act autonomously on. Being that we want our agent to consider time zones I would add the "Created (date/time)" and "Location" form elements to the incident form to capture that data. Also, if the priority is critical I would have a UI Policy that makes the Description field mandatory. With the addition of those form elements and UI Policy the incident form should now capture the necessary data.

Now I would determine additional data the agent would need. The AI Agent would need access to the company’s engineers and their individual schedules and locations. If there wasn’t already a table dedicated to this, I’d create a table called the “Company Engineers Table” with the following fields: Employee Name, Email, Position, Level, Timezone, Workload and Availability. The values for level would be determined based on the position (Ex. Junior Engineer would correspond to Level 1 while Staff Engineer would correspond to Level 3). The values for the availability field might be referenced from an HR related table or I’d have to compile that data myself. I would also create an additional table called the “Critical Reference Table”. The purpose of this table would be for the agent to reference when it’s time to locate an engineer. The fields for this table would be Employee Name, Level, Short Description (short description from the incident), Description (description from the incident), Workload(reference field from Company Engineers Table) and Timezone. Once those tables are in place I would create a business rule that runs when the Company Engineer Table is queried. The purpose of this business rule would be to check the current workload each engineer has that is documented within the ServiceNow instance then assign a value of “light”, “medium” or “heavy” based on a personal algorithm. An example of identifying workloads might be to determine how many incidents are currently assigned to an engineer. I would also create two additional business rules, also set to run when the Company Engineers Table and Critical Reference Table are queried to check the active status of each engineer via the sys_user table, and remove the records of any inactive engineers.

At this point the incident form is appropriately configured and the engineer and critical reference tables are in place and current. Now I’d be ready to map out the nodes of the agent and workflow. The workflow would be triggered by an incident created with a critical priority. Once the workflow begins it would pass control and incident data over to the agent. The agent from there would be designed to do the following:

1.  Query the Company Engineers Table and Critical Reference Table to trigger the associated business rules.
2.  Determine the timezone the incident was created in based on the time and location data.
3.  Reference the Critical Reference table to see if it can streamline the engineer selection process.

* * *

* Steps 3-5 run if the agent didn’t find a viable option via the Critical Reference Table*

3.  Filter out the engineers from the Company Engineers Table who are unavailable.
4.  Filter out engineers not in the incident timezone.
5.  Return the list of remaining engineers.

* If the agent runs the above filters and discovers there are no available engineers in the incident timezone it would re-do the steps but instead of filtering out engineers not in the incident timezone it would prioritize engineers in closest proximity to the incident timezone.

Now the agent should have a short list of available engineers that can remedy the issue. I would instruct the agent to send the email to the engineer most suited for the job. The agent would take into consideration level and current workload as key factors in making the decision. After the engineer has been successfully notified of the incident the agent would add a record to the Critical Reference table. Now in the future the agent has more context about what types of incidents different engineers have handled in the past.

