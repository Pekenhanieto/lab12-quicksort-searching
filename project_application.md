# Lab 12 - Project Application

## E1 - Filter and ownership
Identify selected and excluded IDs, their reasons, and who owns each object.

Selected IDs: Records meeting the specific delivery criteria (such as completed or active orders) are selected into the temporary delivery report view.

Excluded IDs: Records failing the filter criteria (such as pending or cancelled orders) are excluded from the temporary view.

Reasons: Filtering allows management to generate targeted reports and perform audits on relevant subsets of data without modifying the master operational database.

Object Ownership: The master collection owns the memory allocation for all delivery records. The temporary report view only stores borrowed pointers (Parcel *) to these objects, meaning it must never use delete on them, preventing memory corruption or double-free errors.

## E2 - Report order and algorithm
List the observed report IDs and explain how each comparison rule affects them.
State the sorting algorithm used and its time and auxiliary-space bounds.

Observed Report IDs: The delivery report displays the sorted pointer view, organizing records in ascending order by their target key (e.g., orderId or timestamp).Comparison Rules: Comparison rules establish whether sorting is ascending or descending, and how ties are handled, ensuring consistent and deterministic placement.Algorithm & Bounds: Uses Quicksort applied to a pointer view.Time Complexity: O(n \log n) average and best case, O(n^2) worst case.Auxiliary Space: O(\log n) for the recursive call stack under balanced partitions, plus O(n) auxiliary space to store the pointer view itself.

## E3 - Lookup choice and tests
Explain why this report cannot support binary search by orderId as currently sorted.
Give one found, one excluded, and one nonexistent test with observed results.
Describe what an additional ID-sorted view would cost and enable.

Lookup Limitation: If the delivery report view is sorted by a different field (or left unsorted), binary search cannot be applied directly by orderId. Searching requires either a linear scan over the view or maintaining a separate view sorted specifically by orderId.Test Results:Found Test: Queried a valid orderId present in the report, successfully retrieving the record details.Excluded Test: Queried an order ID that was filtered out during report generation, correctly returning a "not found" status.Nonexistent Test: Queried an arbitrary missing ID, safely returning -1 without out-of-bounds errors.Additional View Cost & Enablement: Creating an additional ID-sorted view costs O(n) extra memory for pointers and O(n \log n) sorting time, but it enables rapid O(\log n) binary search lookups directly by order ID.E4 - Evidence and project connection

## E4 - Evidence and project connection
Source: delivery_report_practice.cpp
Output: delivery_report_output.txt
Explain how you verified that original records kept their order and field values.
Explain how this separation protects the operational FIFO sequence in the project.

Source & Output Files: Implemented in delivery_report_practice.cpp with execution captured in delivery_report_output.txt.

Order & Field Verification: Verified by printing the original collection before and after generating and sorting the temporary view, confirming that all original records preserved their exact index positions, sequence, and field values.

Operational FIFO Protection: This separation protects the core operational queue and FIFO sequence from accidental reordering, corruption, or side effects during intensive search and reporting tasks.