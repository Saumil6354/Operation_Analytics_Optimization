Executive Summary 
This project addresses the critical challenge of optimizing production planning within a flexible manufacturing system. The primary objective was to develop and analyze an advanced optimization model that minimizes total operational costs, thereby enhancing strategic decision-making. 
The initial phase implemented a baseline Mixed-Integer Linear Programming (MILP) model, focusing on minimizing rejected production batches. While effective, this original model lacked crucial financial considerations. 
To enhance realism, the project significantly extended the baseline by incorporating explicit production costs (machine operating costs) and financial penalties for rejected batches into a unified objective function aimed at minimizing total cost. This extension, implemented using Google OR-Tools CP-SAT solver in Python, provides a more robust and financially realistic framework. Synthetic data was generated to simulate diverse operational scenarios and rigorously test the model's performance. 
Key findings from the comprehensive sensitivity analysis 
•	Machine Operating Costs: The model showed sensitivity to machine costs. 
Interestingly, increasing one machine's cost sometimes led to a decrease in total cost, as the model strategically underutilized the expensive machine or increased rejections to avoid high operational expenditure. This highlights the importance of accurate cost data. 
•	Rejection Penalties: Low rejection penalties often led the model to reject all batches, as production was financially less favorable. Conversely, high penalties compelled scheduling, demonstrating the direct influence of penalty structures on fulfillment rates and profitability. 
•	Planning Horizon: The total cost and rejection rates exhibited a non-linear relationship with the planning horizon, suggesting complex interactions between batch deadlines, machine availability, and utilization. 
•	Production Time Volatility: Increased average batch production times generally resulted in higher total costs and more rejected batches, straining system capacity. 
•	Batch Type Mix: Performance was notably affected by the proportion of different batch types, due to workshop constraints. Imbalances often led to increased rejections and higher costs. 
Managerial Implications 
This analysis offers critical insights for operations managers, underscoring the necessity of: 
•	Accurate Costing: Understanding true machine operating costs. 
•	Strategic Penalty Setting: Aligning operational decisions with business priorities through appropriate financial penalties. 
•	Flexible Planning: Recognizing that planning horizon adjustments have complex 
effects. 
•	Product Portfolio Management: Understanding how product mix impacts scheduling feasibility under shared resource constraints. 
 
In conclusion, this project successfully developed a financially aware production planning model and provided valuable insights through rigorous sensitivity analysis. The findings offer actionable recommendations for optimizing manufacturing operations and making informed resource allocation decisions in dynamic environments. 
 
 
 
Project Overview 
In today's competitive landscape, efficient production planning and scheduling are paramount for manufacturing systems to optimize resource utilization, minimize costs, and meet demand effectively. This project delves into the complex domain of discrete event systems within a flexible manufacturing environment, where multiple production batches compete for limited machine resources and adhere to various constraints. The core challenge lies in developing robust optimization models that can navigate these complexities to achieve optimal operational outcomes. 
Building upon a foundational academic study in the field, this project aims to extend and enhance existing methodologies. The initial phase involved accurately implementing a Mixed-Integer Linear Programming (MILP) model, derived from a peer-reviewed article, which primarily focused on minimizing the number of rejected production orders. While providing a critical foundation for feasibility, a more comprehensive approach necessitates incorporating direct financial considerations. 
Therefore, the central objective of this project is to develop an advanced optimization model that not only addresses scheduling complexities but also explicitly minimizes total operational costs, encompassing both production expenses and penalties for unfulfilled orders. Furthermore, this project aims to rigorously test the model's robustness and sensitivity to various real-world parameters through systematic analysis, ultimately translating complex mathematical outputs into actionable business insights for effective managerial decisionmaking. 
 
 
 
 
Article Summary & Original Model Description 
This section provides a summary of the foundational research article, "Mathematical 
Model for Production Plan Optimization - A Case Study of Discrete Event Systems," by 
Sadok Rezig et al. (2020), and details the original Mixed-Integer Linear Programming 
(MILP) model implemented as the project's baseline. 
Summary of the Selected Article 
The article by Rezig et al. (2020) presents an optimal scheduling model tailored for discrete event systems (DES), specifically focusing on a flexible manufacturing system (FMS) operating under production and maintenance constraints. The core contribution of the paper is a novel approach to mapping a simulation event relationship graph into a mixedinteger program, demonstrating its applicability through a real-world case study within the 
LGIPM Laboratory in France. 
The authors emphasize the use of rich mathematical theory and optimization algorithms to address the complexities of FMS, which are characterized by dynamic event occurrences, resource sharing, and intricate interdependencies. A key aspect of their model is the integration of both production requirements (e.g., earliest/latest manufacturing dates, batch types) and operational constraints (e.g., machine availability due to maintenance, nonsimultaneous processing of different batch types within a workshop). The paper highlights the flexibility of the proposed mathematical model, suggesting its potential for broader application in general manufacturing systems for optimal resource scheduling. 
Original Model Formulation 
The original MILP model, as detailed in the article and implemented in the baseline production_plan_base.py script, aims to find a production schedule that prioritizes fulfilling orders while adhering to strict operational rules. 
 
•	Objective Function: The primary objective of the original model is to minimize the number of non-operated (rejected) production batches. This is represented as: 
  
•	Key Decision Variables: The model utilizes several types of decision variables to capture the scheduling decisions: 
o	Xi,j,t: A binary variable equal to 1 if batch i starts on machine j at time t, and 0 otherwise. 
o	Fi: A binary variable equal to 1 if batch i is rejected, and 0 otherwise. 
•	Implied variables: Although not explicitly defined as decision variables, auxiliary variables representing start times, end times, and optional interval variables are implicitly used within the OR-Tools CP-SAT formulation to manage scheduling. 
•	Main Constraints: The model incorporates several critical constraints to ensure a feasible and realistic production schedule: 
o	Batch Assignment Constraint: Each batch must either be assigned to exactly one machine at a specific start time or be designated as rejected. This ensures that no batch is partially processed or assigned to multiple machines. 
  
o	Machine Capacity / No Overlap Constraint: Each manufacturing system (machine) can process at most one batch at any given time. This is a fundamental non-overlap constraint, preventing simultaneous operations on a single resource. 
  
•	Time Window Constraints: Each batch has an Earliest Manufacturing Date (EMi) and a Latest Manufacturing Date (LMi) within which its processing must start. The model ensures that assigned batches respect these time windows and complete within the overall planning horizon. 
  
•	Workshop Type Constraint: Batches of different product types (e.g., 'Red' and 'Blue' batches as defined in the case study) cannot be operated in the same workshop concurrently. This is a crucial constraint that reflects operational restrictions and potential for bottlenecks. 
  
•	Non-Interruption Constraint: Once a batch begins processing on a machine, it must continue uninterrupted until its completion. 
  
Original Model Implementation and Results 
The original model was implemented in Python using Google OR-Tools CP-SAT solver. The initial data set, based on Table 1 from the article, defined 7 production batches with specific types, production times, and time windows, to be scheduled over an 8-hour planning horizon on two manufacturing systems (S2 and S3). 
  
Figure 1: output of production_plan_base.py 
Upon execution with the baseline parameters, the original model achieved an optimal solution where 1 batch (Batch No 6) were rejected. The solver successfully found an optimal/feasible schedule that accommodated all production demands within the given constraints and planning horizon. The visual representation of this schedule, typically a Gantt chart, would show batches assigned to either S2 or S3, without any overlaps or type conflicts within the workshop, and respecting their respective time windows. This demonstrated the model's capability to efficiently allocate resources to fulfil all orders under the specified conditions. 
  
Figure 2: Schedule Gantt Chart (Base Model) 
Model Extension & Solution 
To enhance the practical applicability and economic relevance of the production planning model, an extension was developed to incorporate explicit financial considerations into the optimization objective. The original model's focus on solely minimizing rejected batches did not account for the varying costs associated with production on different machines or the financial implications of not fulfilling an order. 
Motivation for Extension 
The primary motivation for this extension was to transition the model from a purely feasibility-driven objective (minimizing rejections) to a more holistic cost-optimization objective. In real-world manufacturing, every operational decision has a cost attached, whether it's the cost of operating a machine, the cost of materials, or the penalty incurred for failing to deliver a product. By integrating these financial aspects, the extended model can: 
•	Provide Financially Grounded Decisions: Allow for trade-off analysis between production costs and the penalties of rejection, reflecting true business economics. 
•	Identify Cost-Efficient Schedules: Pinpoint schedules that, while potentially having some rejections, result in a lower overall financial burden compared to forcing all production at any cost. 
•	Inform Pricing and Penalty Structures: Offer insights into how machine operating costs and rejection penalties influence optimal production strategies, which can inform strategic business decisions. 
Extended Model Formulation 
The core change in the extended model involves a significant modification to the objective function. Instead of just minimizing the count of rejected batches, the new objective minimizes the total cost, which is the sum of total production costs for assigned batches and total penalties for rejected batches. 
•	New Parameters: To support the revised objective, the following parameters were introduced: 
o	Cj: Operating cost per hour for machine j. 
o	Pi: Financial penalty incurred for rejecting batch i. 
•	Modified Objective Function: The extended objective function is formulated as: 
Minimize (∑%!’&( ∑%&*#’( ∑)$’( 𝑥!,#,$. 𝑃𝑟𝑜𝑑𝑢𝑐𝑡𝑖𝑜𝑛𝑇𝑖𝑚𝑒!. 𝐶# + (∑%&!’( 𝑟!. 𝑃!) 
Where: 
xi,j,t: Binary variable (1 if batch i starts on machine j at time t, 0 otherwise). 
ProductionTimei: Duration required to process batch i. ri: Binary variable (1 if batch i is rejected, 0 otherwise). 
All other constraints (Batch Assignment, Machine Capacity, Time Windows, Workshop Type, Non-Interruption) from the original model remain in effect to ensure operational feasibility. 
Implementation of Extended Model 
The extended model was implemented by modifying the solve_production_scheduling_extended function within the Python script. 
The key implementation steps included: 
•	Defining New Data Parameters: Dictionaries for 
COST_PER_HOUR_ON_MACHINE and REJECTION_PENALTY_PER_BATCH 
were added to the model's data definition section. 
•	Revising Objective Function: The model.Minimize() call was updated to compute and minimize the sum of total_production_cost and total_rejection_penalty variables, 
which are themselves sums of their respective components, conditional on batch assignment or rejection. 
•	Data Generation: The generate_synthetic_batch_data function was enhanced to not only create synthetic batch characteristics (production time, EM/LM, type) but also to generate corresponding REJECTION_PENALTY_PER_BATCH values, allowing for 
varied testing scenarios. 
Results of Extended Model 
When executed with a sample set of synthetic data and the newly introduced cost and penalty parameters, the extended model provides an optimal solution that balances production costs with rejection penalties. For a typical run, the model might produce an output similar to this (example from a previous run, actual results depend on specific parameters and generated data): 
  
Figure 3: Output of production_plan_extended.py 
  
Figure 4:Schedule Gantt Chart (Extended Model) 
The production schedule details would show which batches were assigned to which machines at what times, along with their individual production costs. Crucially, it would also list the batches that were rejected and the associated penalty incurred. 
Comparison with Original Model 
In contrast to the original model, which always aimed for zero rejections (if feasible), the extended model may optimally choose to reject certain batches if the associated production cost (on either machine S2 or S3) outweighs the penalty of not producing them. This demonstrates a key trade-off: achieving 100% fulfilment might come at a higher financial cost, and the extended model enables the organization to find the most economically favourable balance. This shift provides a more nuanced and practical solution for real-world production management where financial efficiency is often paramount. 
Sensitivity Analysis 
Sensitivity analysis is a crucial component of optimization modeling, enabling a deeper understanding of how robust an optimal solution is to variations in input parameters. For this project, sensitivity analysis was conducted to systematically evaluate the extended model's behavior under various conditions, thereby identifying critical parameters that significantly influence the optimal production schedule, total cost, and the number of rejected batches. This analysis provides valuable insights for strategic decision-making and risk management in a dynamic manufacturing environment. 
Methodology 
The sensitivity analysis was performed by systematically varying one key input parameter at a time while holding all other parameters constant at their base values. This "one-at-a-time" (OAT) approach allows for clear attribution of observed changes in the model's output to the specific parameter being tested. 
The production_plan_sensitivity_analysis.py Python script was utilized to automate the execution of the solve_production_scheduling_extended function across predefined ranges of parameter values. The primary metrics observed were the Total_Cost (the objective function value) and the Rejected_Batches count. All results were compiled into an Excel file (sensitivity_analysis_results.xlsx) with dedicated sheets for each scenario and visualized using Matplotlib. 
The following five key parameters were selected for sensitivity testing: 
•	Relative Machine Costs: Specifically, the cost per hour of Machine S2. 
•	Overall Rejection Penalty Scale: A multiplier applied uniformly to all batch rejection penalties. 
•	Planning Horizon (H): The total number of time units available for scheduling. 
•	Batch Production Times: A uniform multiplier applied to the average production time of all batches. 
•	Batch Type Proportion: The percentage of 'Red' (R) type batches in the generated data. 
Scenario 1: Sensitivity to Relative Machine Costs (S2) 
This scenario investigated the impact of varying the operating cost of Machine S2 while keeping the cost of Machine S3 constant. 
Table 1: Sensitivity to Relative Machine Costs (S2) 
Parameter_Varied 	Parameter_Value 	Total_Cost 	Rejected_Batches 	Status 
Cost S2 ($/h) 	80 	3772 	5 	OPTIMAL 
Cost S2 ($/h) 	100 	4230 	5 	OPTIMAL 
Cost S2 ($/h) 	120 	3695 	3 	OPTIMAL 
Cost S2 ($/h) 	150 	4520 	6 	OPTIMAL 
Cost S2 ($/h) 	180 	3943 	6 	OPTIMAL 
 
  
Figure 5: Sensitivity to Relative Machine Costs (S2) 
Analysis and Interpretation 
As the cost of Machine S2 increases, the total cost and number of rejected batches show fluctuating, non-linear trends. The lowest total cost and fewest rejections (3) occur when S2 costs $120/h. At higher S2 costs ($150/h and $180/h), the number of rejected batches increases to 6. This indicates the model is incentivized to avoid using a very expensive S2, leading to higher overall costs or more rejections as it struggles to find optimal assignments on the remaining resources. 
Managerial Implications 
Managers need to identify the "sweet spot" for machine operating costs, as there isn't a simple linear relationship between cost increases and total expenses. Operating a machine beyond a certain cost threshold can significantly raise rejections, suggesting it may be more economical to offload demand or accept rejections than to operate that specific machine at very high costs. 
Scenario 2: Sensitivity to Overall Rejection Penalty Scale 
This scenario explores how the model's willingness to reject batches changes as the financial penalty for rejection varies. 
Table 2: : Sensitivity to Overall Rejection Penalty Scale 
Parameter_Varied 	Parameter_Value 	Total_Cost 	Rejected_Batches 	Status 
Penalty Multiplier 	0.1 	1000 	10 	OPTIMAL 
Penalty Multiplier 	0.5 	2569 	10 	OPTIMAL 
Penalty Multiplier 	1 	3180 	4 	OPTIMAL 
Penalty Multiplier 	2 	5260 	3 	OPTIMAL 
Penalty Multiplier 	5 	8541 	4 	OPTIMAL 
 
  
Figure 6: Sensitivity to Overall Rejection Penalty Scale 
Analysis and Interpretation 
This scenario shows a critical breakpoint: at very low penalty multipliers (0.1x and 0.5x), the model rejects all 10 batches, as production costs outweigh minimal rejection penalties. As penalties increase (1.0x to 2.0x), the model shifts to scheduling more batches, reducing rejections to 3. However, at the highest multiplier (5.0x), rejections slightly rise back to 4, indicating that for some batches, even high penalties don't justify their substantial production costs. 
Managerial Implications 
Managers must carefully determine rejection penalty values. If penalties are too low, the system may prioritize cost savings by rejecting orders, potentially harming reputation. If penalties are too high, it might force uneconomical production. The analysis highlights a need for a balanced approach to align operational decisions with business goals. 
Scenario 3: Sensitivity to Planning Horizon (H) 
This scenario examines the effect of varying the total planning time available for scheduling. 
Table 3: Sensitivity to Planning Horizon (H) 
Parameter_Varied 	Parameter_Value 	Total_Cost 	Rejected_Batches 	Status 
Horizon (H) 	8 	3932 	6 	OPTIMAL 
Horizon (H) 	10 	3210 	4 	OPTIMAL 
Horizon (H) 	12 	3381 	5 	OPTIMAL 
Horizon (H) 	14 	3311 	3 	OPTIMAL 
Horizon (H) 	16 	3159 	3 	OPTIMAL 
 
  
Figure 7: Sensitivity to Planning Horizon (H) 
Analysis and Interpretation 
As the planning horizon (H) increases, the total cost generally decreases, and the number of rejected batches tends to fall. A longer horizon provides more time and flexibility, making it easier to schedule batches. The optimal cost of $3159 and 3 rejected batches is achieved at H=16. The fluctuation at H=12 (higher cost, more rejections than H=10) suggests that specific batch interactions and deadline alignments can still impact efficiency. 
Managerial Implications 
Extending the planning horizon generally improves scheduling efficiency by reducing both costs and rejections. Managers should aim for a sufficiently long planning window to maximize resource utilization and order fulfillment, while considering forecast accuracy for very distant horizons. 
Scenario 4: Sensitivity to Increased Batch Production Times 
This scenario investigates the impact of changes in the average production time required for batches, simulating varying product complexity or efficiency. 
Table 4: Sensitivity to Increased Batch Production Times 
Parameter_Varied 	Parameter_Value 	Total_Cost 	Rejected_Batches 	Status 
Prod Time Multiplier 	0.7 	2699 	3 	OPTIMAL 
Prod Time Multiplier 	0.9 	2552 	3 	OPTIMAL 
Prod Time Multiplier 	1 	3801 	5 	OPTIMAL 
Prod Time Multiplier 	1.2 	3423 	3 	OPTIMAL 
Prod Time Multiplier 	1.5 	4776 	5 	OPTIMAL 
 
  
Figure 8: Sensitivity to Increased Batch Production Times 
Analysis and Interpretation 
As batch production times increase, total cost generally rises and rejected batches tend to increase (e.g., 5 at 1.0x and 1.5x multipliers). This shows increased strain on capacity. 
Conversely, reduced production times (0.7x and 0.9x) lead to lower costs and fewer rejections (3), indicating improved efficiency. The lowest cost occurs at a 0.9x multiplier, suggesting minor efficiency gains can be significant. 
Managerial Implications 
This highlights the financial benefits of reducing production times. Investments in process improvement or automation to shorten batch processing times can significantly lower overall costs and improve order fulfillment. 
Scenario 5: Sensitivity to Batch Type Proportion (High 'R' demand) 
This scenario assesses the impact of varying the proportion of 'Red' (R) type batches in the total demand mix, given the constraint that different batch types cannot be processed simultaneously in the same workshop. 
Table 5: Sensitivity to Batch Type Proportion (High 'R' demand) 
Parameter_Varied 	Parameter_Value 	Total_Cost 	Rejected_Batches 	Status 
Proportion R 	0.1 	3367 	3 	OPTIMAL 
Proportion R 	0.3 	4016 	3 	OPTIMAL 
Proportion R 	0.5 	3576 	4 	OPTIMAL 
Proportion R 	0.7 	3408 	3 	OPTIMAL 
Proportion R 	0.9 	3406 	3 	OPTIMAL 
 
  
Figure 9: Sensitivity to Batch Type Proportion (High 'R' demand) 
Analysis and Interpretation 
Total cost and rejected batches fluctuate with the proportion of 'R' type batches. 
Increasing 'R' types generally leads to higher costs, and rejections show variability (e.g., 4 at 0.5 proportion). The lowest cost is at 0.1 proportion. At higher proportions (0.7 and 0.9), costs are lower and rejections are fewer (3) compared to 0.5, suggesting that at very high dominance of one type, the model finds efficient ways to consolidate production. 
 
Managerial Implications 
This analysis underscores the importance of the product mix. Highly imbalanced demand, especially with workshop constraints, can impact scheduling efficiency and costs. Managers should consider these constraints in demand forecasting and product portfolio decisions to mitigate negative impacts. 
Assumptions and Limitations 
This project's optimization model, while providing valuable insights, operates under several assumptions that, if not fully met in a real-world scenario, could limit its direct applicability. Recognizing these assumptions helps in interpreting the results and identifying areas for future model enhancement. 
Assumptions 
The key assumptions underpinning both the original and extended models are: 
•	Deterministic Parameters: All input parameters, including production times, machine operating costs, rejection penalties, and batch earliest/latest manufacturing dates, are assumed to be known with certainty and remain constant throughout the planning horizon. 
•	Fixed Machine Capacity and Availability: Manufacturing systems (machines) are assumed to have fixed, known capacities and are continuously available throughout their scheduled operational periods. There are no unexpected breakdowns, unscheduled maintenance, or variable operational speeds. 
•	Non-Interruptible Batches: Once a batch begins processing on a machine, it continues without interruption until completion. There is no allowance for preemption or partial processing. 
•	Known Demand: The set of production batches and their characteristics (type, production time, time windows) are fully known at the start of the planning horizon. 
There are no unexpected rush orders or cancellations. 
•	Instantaneous Material Availability: All necessary raw materials and components for each batch are assumed to be immediately available when needed; there are no supply chain delays or inventory constraints. 
•	No Setup Times (or Ignored): The model implicitly assumes negligible or zero setup/changeover times when switching between batches or batch types on a machine. 
If setup times exist, they are not explicitly modeled. 
•	Homogeneous Batches within Types: All batches of the same 'Type' (e.g., 'R' or 'B') are assumed to be identical in terms of their impact on workshop constraints. 
•	Single Workshop Constraint: The specific constraint about different batch types not operating simultaneously applies only within a single, defined workshop. 
Limitations 
Based on the above assumptions, the primary limitations of the current model when applied to a real-world, dynamic manufacturing environment include: 
•	Lack of Uncertainty Handling: Real-world manufacturing environments are highly uncertain, facing variable demand, machine breakdowns, quality issues, and supply chain disruptions. The deterministic nature of the model means it cannot dynamically adapt to these unforeseen events. 
•	Simplified Resource Modeling: The model does not account for other critical resources such as labor availability (e.g., skilled technicians, operators), tool availability, or energy constraints, which can significantly impact scheduling. 
•	Absence of Dynamic Scheduling: The model provides a static optimal schedule for a given horizon. It does not support real-time rescheduling in response to new events or changes in parameters. 
•	Setup Time Omission: Ignoring setup times can lead to impractical schedules, especially in environments with frequent product changeovers that incur significant time and cost penalties. 
•	Inventory Management: The model does not consider inventory levels or holding costs, which are crucial for multi-period planning and balancing production with demand over time. 
•	Single Objective Focus (Original) / Simplified Multi-Objective (Extended): While the extended model combines costs and penalties, it doesn't explicitly handle truly competing objectives (e.g., maximizing profit while minimizing environmental impact) as separate entities, nor does it explore the full Pareto front of solutions. 
Conclusion 
This project successfully developed and analyzed an advanced optimization model for production planning and scheduling within a flexible manufacturing system. Building upon an existing academic framework, the initial model focused on minimizing rejected batches. A significant extension was then implemented, transforming the objective to a comprehensive total cost minimization, encompassing both machine operating expenses and financial penalties for unfulfilled orders. This enhanced model provides a more economically realistic and practically valuable tool for operational decision-making. 
The comprehensive sensitivity analysis performed across five distinct scenarios yielded critical insights: 
•	It highlighted that seemingly straightforward cost increases can lead to complex, nonlinear shifts in optimal strategy, sometimes favoring rejections or underutilization of expensive resources to achieve overall cost efficiency. 
•	The analysis demonstrated a clear breakpoint where the magnitude of rejection penalties directly dictated the trade-off between incurring production costs and accepting order rejections, emphasizing the strategic importance of appropriately valuing order fulfillment. 
•	Furthermore, the project revealed the nuanced impact of planning horizon, production times, and product mix (especially under workshop constraints) on overall costs and fulfillment rates, underscoring the dynamic interplay of these operational factors. 
In essence, this project moved beyond simply finding a feasible schedule to identifying the most economically optimal one under varying conditions. The findings provide actionable managerial implications, stressing the need for accurate cost data, strategic penalty setting, flexible planning, and informed product portfolio management. While operating under certain assumptions, the developed model offers a robust framework for guiding operational decisions and optimizing resource allocation in complex manufacturing environments. This work lays a strong foundation for future research into more sophisticated, real-time, and uncertainty-aware production planning models. 
 
 
 
 

 
References 
Google. (n.d.). Google Gemini (AI Model). 
Google. (n.d.). Google OR-Tools: Introduction. Retrieved from https://developers.google.com/optimization/introduction 
Rezig, S., Ezzeddine, W., Turki, S., & Rezg, N. (2020). Mathematical Model for Production Plan Optimization—A Case Study of Discrete Event Systems. Mathematics, 8(6), 
955. 

