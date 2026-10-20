# Review checklist

Answer yes or no for each line. Fix every "no" before presenting. Lines tagged (system) apply only to system-size designs.

- Client confirmed the requirements, or the design is marked unattended?
- Every assumption labeled?
- Out of scope listed?
- (system) Scale math shown with units, and the hard part named?
- Every diagram the steps call for (architecture, class, and one sequence per deep dive) drawn as an SVG file, embedded in the doc, and checked by eye?
- Every piece of state has exactly one named owner?
- Retry-safe calls marked, with how?
- Every component and dependency has a failure case?
- What the user sees in each failure case?
- Security and privacy risks covered, each with a mitigation?
- Each major choice has options, a reason, and a cost?
- Build vs buy considered?
- (system) Every hard part has a test, metric, or drill that proves it?
- Client constraints respected?
- Open risks, and what would change the design, listed at the end?
