# Goal
With branching and branch testability now being supported for Flutter & Dart releases, cherrypicking fixes will now be the preferred method to address issues for released software (beta and stable channels.)  Stability of the release will be the overarching goal, so only high impactful and critical cherrypicks will be allowed across Dart and Flutter.  This document outlines the process for requesting and approval of cherrypicks. 

**Note: This process applies to bugs and regressions.  Feature work is not considered for cherrypicking and will need to wait for the next release.**

# The Cherrypick “Committee”
**The Cherrypick “committee”** will initially be composed of leads from product, engineering and program managers across Dart and Flutter.   They will meet/discuss asynchronously on an as-needed basis assuming there are requests to review or prioritize.  Longer term, we may include contributors as well.  The individual (Dart, Flutter) groups will meet to review cherrypick requests from their repos as needed, organized by their TPMs.  The full group will meet when cherrypicks are approved by the individual groups and consideration for a hotfix release is required.

As we get into the stable release period, the unified group will still need to come to consensus for approval of **all** cherrypicks, even if they are for a certain area, as risk will need to be assessed across Dart and Flutter.

# Process Flow

Assuming X.Y has been released in beta or stable, labels are created as “**cp: X.Y**” by the TPM team.

1. Issue/bug is filed 
    1. “X.Y Cherrypick Request” can be added here by filer or community
1. Issue enters triage process
    1. Top level triage	
       1. Applies appropriate team label
       1. Can apply “X.Y Cherrypick Request” label if it appears to have appropriate severity or impact.
    1. Engineering triage
       1. Milestone applied
       1. Can apply “X.Y Cherrypick Request” label if it appears to have appropriate severity or impact.
       1. If Cherrypick request label is on the issue, agree on severity/impact and prioritization with the Cherrypick review
  1. **Issue is fixed and checked into master.**
     1. Pre and post-submit tests executed as normal for verification.
     1. Cherrypick committees may influence prioritization depending on the severity of the issue during the bug fixing process.
1. Fixed issue with “X.Y Cherrypick Request” is reviewed by the individual Cherrypick reviewer(s).
   1. TPgM/PM/Eng involved to make a decision based on factors that are different for beta and stable and the age of the branch.
      1. **Impact criteria:** 
         1. Early beta stabilization (first 2 weeks of beta): High impact P1/P0 affecting multiple customers / would be a P0 in stable / regressions / security issues
         1. Late beta stabilization: P0 issues / P1 issues affecting larger customers / regressions / security issues
         1. Stable: P0 issue, affecting large base of customers, high % of user apps | importance factor / security issues
            1. crashing on x% of phone ecosystem
            1. breaking developer flow with no easy workaround
       1. **Risk criteria:**
            1. Is it a manual or automated merge?
            1. How many lines of code, what areas of the stack is it touching?
            1. Testability of the cherrypick. 
       1. **Release timing**
            1. How close is the next beta/stable release?
1. If the cherrypick is deemed necessary and approved by the individual committee, the full committee (Flutter+Dart) will then review using the same factors as above, with attention to full stack risk.
1. If denied, the issue is updated with denial reason and ideally a target release.
1. If approved
   1. TPM/Developer will create PR into the vX.Y.Z branch
      1. for Dart, this will be the beta branch until the vX.Y.Z branch support or similar is built.
      1. If pre-submit tests fail, a risk assessment will need to be performed with input from the cherrypick committee.
   1. In case of manual merge, re-review of PR will be needed (preferably by the original engineer, or TL.)
   1. Post-submit tests are triggered on branch
      1. In case of multiple cherrypicks/issues this will be done after a batch
1. When tests pass
   1. when there is a lull in CPs / or a priority CP has been merged, a new release will be made available through the release process.
