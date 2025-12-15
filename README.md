### How to Execute the DynamoDB PITR Recovery Runbooks

  * This section explains only how to execute each runbook after it has been deployed via CloudFormation.

#### Prerequisites (Both Runbooks)

* CloudFormation stack is successfully deployed

* You have access to AWS Systems Manager → Automation

* You can assume or specify the Approver IAM principal

* DynamoDB PITR is enabled (or the runbook will enable it automatically where supported)

####  Execute: DynamoDB PITR Restore & Scalable Data Copy

* Use when: You want to restore data from PITR and copy it back to the original table (non-destructive).

#### Steps

* Open AWS Console → Systems Manager → Automation

* Select the document:

* DDB_PITR_Restore_And_Selective_Copy_V11

* Click Execute automation

* Provide the required parameters:

* OriginalTableName – Name of the table to recover

* PITRTimestamp – Point-in-time timestamp (UTC, ISO format)

* DataCopyType – FULL or FILTERED

* FilterField – Required only if FILTERED

* FilterValueOrRange – Required only if FILTERED

* ApproverPrincipalArn – IAM principal authorized to approve

* Start execution

* When prompted, approve the restore timestamp

* When prompted again, approve the data copy operation

#### Monitor progress:

###### SSM Automation execution status

* Step Functions execution (linked in execution output)

* The runbook completes when the Step Function finishes copying all data.

####  Execute: DynamoDB PITR Table Replace (Destructive)

* Use when: You need to fully replace a table from a PITR backup (delete + restore).

#### ⚠️ Warning

* This runbook deletes and replaces the original table.

##### Steps

* Open AWS Console → Systems Manager → Automation

* Select the document:

* DDB_ROLLBACK_SELF_HEAL_V8

* Click Execute automation

* Provide the required parameters:

* OriginalTableName – Table to be replaced

* PITRTimestamp – Point-in-time timestamp (UTC, ISO format)

* BackupTableNameIfDeleted – Optional (only if original table was already deleted)

* ApproverPrincipalArn – IAM principal authorized to approve

* Start execution

* Review the approval message carefully

* Approve only after confirming:

* Correct table name

* Correct PITR timestamp

* Monitor execution in SSM Automation until completion

* The runbook completes after the original table is deleted, restored, and configuration is reapplied.

* Execution Notes

* All approvals are mandatory

* All actions are audited via CloudTrail

* Long-running operations may take hours for large tables

* Do not re-run while an execution is in progress
