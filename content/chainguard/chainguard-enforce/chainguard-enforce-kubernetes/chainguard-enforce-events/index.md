---
title : "Chainguard Enforce Events"
lead: ""
description: "Chainguard Enforce Events"
type: "article"
date: 2022-11-15T12:05:04
lastmod: 2023-03-28T14:50:13
draft: false
tags: ["Enforce", "Reference", "Product"]
images: []
weight: 780
---

## Introduction

Chainguard Enforce generates and emits [CloudEvents](https://cloudevents.io/) based on actions that occur within a Chainguard account, such as registering a Kubernetes cluster or creating an IAM invitation. Enforce also emits events when workloads or policies are changed in a cluster.

See the [chainguard-dev/enforce-events](https://github.com/chainguard-dev/enforce-events) GitHub repository for two sample applications that demonstrate how to create Slack notifications, as well as how to open a GitHub issue from received events.

To subscribe to Enforce events for your account, use the `chainctl` command like this:

```
chainctl events subscriptions create –group $YOUR_GROUP https://<Your webhook URL>
```

Once you are subscribed to Chainguard Enforce events, you will start receiving HTTP POST requests. Each request has a common set of CloudEvent header fields, denoted by the `Ce-` prefix. The event body is encoded using JSON and will have two top-level keys, `actor` and `body`.

The `actor` field is the identity of the actor in your Enforce account that triggered the event, such as a team member or a Kubernetes cluster. The `body` field contains the specific data about the event, for example the response status for an invite creation request, or a cluster delete request.

The following list of services and methods show example HTTP headers and bodies for public facing Enforce events.

## Service: Admission - Namespace
### Method: Created

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: k8s://namespace-UID
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.32153786Z
Ce-Type: dev.chainguard.admission.namespace.v1
Content-Length: 282
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "change": "created",
    "enforcer_state": "state that policy enforcement is in for the namespace",
    "id": "UIDP of the Namespace (whose parent is the Cluster UIDP)",
    "name": "name of the namespace as it appears in the cluster"
  }
}
```
### Method: Updated

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: k8s://namespace-UID
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.321743378Z
Ce-Type: dev.chainguard.admission.namespace.v1
Content-Length: 282
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "change": "updated",
    "enforcer_state": "state that policy enforcement is in for the namespace",
    "id": "UIDP of the namespace (whose parent is the cluster UIDP)",
    "name": "name of the namespace as it appears in the cluster"
  }
}
```
## Service: Enforce - Admission
### Method: Review

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: k8s://cluster-id
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.321344343Z
Ce-Type: dev.chainguard.admission.v1
Content-Length: 220
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "request": "see https://pkg.go.dev/k8s.io/api/admission/v1#AdmissionRequest",
    "response": "see https://pkg.go.dev/k8s.io/api/admission/v1#AdmissionResponse"
  }
}
```
## Service: Policy - Validation
### Method: Changed

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: k8s://cluster-id
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.320393359Z
Ce-Type: dev.chainguard.policy.validation.changed.v1
Content-Length: 425
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "cluster_id": "cluster identifier",
    "image_id": "image identifier",
    "last_seen": "2023-03-28T14:50:13.320190541Z",
    "policies": {
      "name of the evaluated policy": {
        "change": "Can be [Empty, \"new\", \"degraded\", \"improved\"]",
        "diagnostic": "holds any diagnostic messages surfaced during policy evaluation",
        "last_checked": "2023-03-28T14:50:13.320378957Z",
        "valid": false
      }
    }
  }
}
```
## Service: auth - Auth
### Method: Register

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/auth/v1/register
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.32526359Z
Ce-Type: dev.chainguard.api.auth.registered.v1
Content-Length: 155
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "group": "the group this identity has joined by invitation",
    "identity": "session identity"
  }
}
```
## Service: events - Subscriptions
### Method: Create

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/events/v1/subscriptions
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.327444484Z
Ce-Type: dev.chainguard.api.events.subscription.created.v1
Content-Length: 147
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "id": "identifier of the subscription",
    "sink": "Webhook endpoint (http/https URL)"
  }
}
```
### Method: Delete

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/events/v1/subscriptions
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.327620299Z
Ce-Type: dev.chainguard.api.events.subscription.deleted.v1
Content-Length: 114
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "id": "identifier of the subscription to delete"
  }
}
```
## Service: iam - GroupAccountAssociations
### Method: Create

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/account_associations
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.322386435Z
Ce-Type: dev.chainguard.api.iam.account_associations.created.v1
Content-Length: 386
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "amazon": {
      "account": "Amazon account ID (if applicable)"
    },
    "description": "description of this association",
    "google": {
      "project_id": "Google Cloud Project ID (if applicable)",
      "project_number": "Google Cloud Project Number (if applicable)"
    },
    "group": "group with which this account information is associated",
    "name": "group name"
  }
}
```
### Method: Update

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/account_associations
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.322681161Z
Ce-Type: dev.chainguard.api.iam.account_associations.updated.v1
Content-Length: 337
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "amazon": {
      "account": "amazon account if applicable"
    },
    "description": "group description",
    "google": {
      "project_id": "project id if applicable",
      "project_number": "project number if applicable"
    },
    "group": "group with which this account information is associated",
    "name": "group name"
  }
}
```
### Method: Delete

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/account_associations
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.322913782Z
Ce-Type: dev.chainguard.api.iam.account_associations.deleted.v1
Content-Length: 129
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "group": "UIDP of the group whose associations will be deleted"
  }
}
```
## Service: iam - GroupInvites
### Method: Create

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/group_invites
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.326174071Z
Ce-Type: dev.chainguard.api.iam.group_invite.created.v1
Content-Length: 145
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "expiration": {
      "seconds": 100
    },
    "id": "group UIDP under which this invite resides"
  }
}
```
### Method: Delete

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/group_invites
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.326397891Z
Ce-Type: dev.chainguard.api.iam.group_invite.deleted.v1
Content-Length: 92
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "id": "UIDP of the record"
  }
}
```
## Service: iam - Groups
### Method: Create

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/groups
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.327813316Z
Ce-Type: dev.chainguard.api.iam.group.created.v1
Content-Length: 169
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "description": "group description",
    "id": "group UIDP under which this group resides",
    "name": "group name"
  }
}
```
### Method: Update

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/groups
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.32796873Z
Ce-Type: dev.chainguard.api.iam.group.updated.v1
Content-Length: 169
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "description": "group description",
    "id": "group UIDP under which this group resides",
    "name": "group name"
  }
}
```
### Method: Delete

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/groups
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.328117643Z
Ce-Type: dev.chainguard.api.iam.group.deleted.v1
Content-Length: 92
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "id": "UIDP of the record"
  }
}
```
## Service: iam - Identities
### Method: Create

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/identities
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.323263113Z
Ce-Type: dev.chainguard.api.iam.identity.created.v1
Content-Length: 329
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "identity": {
      "Relationship": null,
      "description": "The human readable description of identity",
      "id": "The unique identifier of this specific identity",
      "name": "The human readable name of identity"
    },
    "parent_id": "The Group UIDP path under which the new Identity resides"
  }
}
```
### Method: Update

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/identities
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.323511335Z
Ce-Type: dev.chainguard.api.iam.identity.updated.v1
Content-Length: 245
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "Relationship": null,
    "description": "The human readable description of identity",
    "id": "The unique identifier of this specific identity",
    "name": "The human readable name of identity"
  }
}
```
### Method: Delete

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/identities
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.323687551Z
Ce-Type: dev.chainguard.api.iam.identity.deleted.v1
Content-Length: 92
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "id": "UIDP of the record"
  }
}
```
## Service: iam - IdentityProviders
### Method: Create

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/identityProviders
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.325588519Z
Ce-Type: dev.chainguard.api.iam.identity_providers.created.v1
Content-Length: 394
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "identity_provider": {
      "Configuration": null,
      "description": "The human readable description of identity provider",
      "id": "The the exact UIDP of the IAM group to nest this identity provider under",
      "name": "The human readable name of identity provider"
    },
    "parent_id": "The exact UIDP of the IAM group to nest this identity provider under"
  }
}
```
### Method: Update

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/identityProviders
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.325809439Z
Ce-Type: dev.chainguard.api.iam.identity_providers.updated.v1
Content-Length: 289
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "Configuration": null,
    "description": "The human readable description of identity provider",
    "id": "The the exact UIDP of the IAM group to nest this identity provider under",
    "name": "The human readable name of identity provider"
  }
}
```
### Method: Delete

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/identityProviders
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.325980054Z
Ce-Type: dev.chainguard.api.iam.identity_providers.deleted.v1
Content-Length: 99
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "id": "The exact UIDP of the IdP"
  }
}
```
## Service: iam - Policies
### Method: Create

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/policies
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.326628611Z
Ce-Type: dev.chainguard.api.iam.policy.created.v1
Content-Length: 284
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "parent_id": "Group UIDP path under which the new policy is associated",
    "policy": {
      "description": "description of the policy",
      "document": "YAML encoded policy document",
      "id": "ID of the policy",
      "name": "name of the policy"
    }
  }
}
```
### Method: Delete

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/policies
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.326861932Z
Ce-Type: dev.chainguard.api.iam.policy.deleted.v1
Content-Length: 90
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "id": "ID of the policy"
  }
}
```
### Method: Update

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/policies
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.327020546Z
Ce-Type: dev.chainguard.api.iam.policy.updated.v1
Content-Length: 202
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "description": "description of the policy",
    "document": "YAML encoded policy document",
    "id": "ID of the policy",
    "name": "name of the policy"
  }
}
```
### Method: ActivateVersion

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/policyVersions
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.327215363Z
Ce-Type: dev.chainguard.api.iam.policy.version.activated.v1
Content-Length: 106
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "version_id": "ID of the policy version"
  }
}
```
## Service: iam - RoleBindings
### Method: Create

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/rolebindings
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.328315361Z
Ce-Type: dev.chainguard.api.iam.rolebindings.created.v1
Content-Length: 261
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "parent": "The Group UIDP path under which the new RoleBinding resides",
    "role_binding": {
      "id": "UID of this role binding",
      "identity": "UID of the Identity to bind",
      "role": "UIDP of the Role to bind"
    }
  }
}
```
### Method: Update

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/rolebindings
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.32852818Z
Ce-Type: dev.chainguard.api.iam.rolebindings.updated.v1
Content-Length: 173
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "id": "UID of this role binding",
    "identity": "UID of the Identity to bind",
    "role": "UIDP of the Role to bind"
  }
}
```
### Method: Delete

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/iam/v1/rolebindings
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.328690194Z
Ce-Type: dev.chainguard.api.iam.rolebindings.deleted.v1
Content-Length: 91
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "id": "UID of the record"
  }
}
```
## Service: tenant - Clusters
### Method: Create

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/tenant/v1/clusters
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.324102087Z
Ce-Type: dev.chainguard.api.tenant.cluster.created.v1
Content-Length: 1700
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "cluster": {
      "activity": {
        "example activity": {
          "controller_name": "the name of the Controller CRD which was the source of this activity on the tenant cluster",
          "last_seen": {
            "nanos": 320189140,
            "seconds": 1680015013
          },
          "namespace": "the namespace in which the source of this cluster activity lives",
          "spec_hash": "the hash of the Controller or Webhook CRD's spec",
          "webhook_name": "the name of the Webhook CRD which was the source of this activity on the tenant cluster"
        }
      },
      "agent_version": "the version of the Chainguard agent last reported by the cluster",
      "auth_info": {
        "client_certificate_data": "Y29udGFpbnMgUEVNLWVuY29kZWQgZGF0YSBmcm9tIGEgY2xpZW50IGNlcnQgZmlsZSBmb3IgVExT",
        "client_key_data": "Y29udGFpbnMgUEVNLWVuY29kZWQgZGF0YSBmcm9tIGEgY2xpZW50IGtleSBmaWxlIGZvciBUTFM="
      },
      "description": "a short description of this cluster",
      "group": {
        "description": "human readable description of group",
        "id": "group UIDP under which this group resides",
        "name": "human readable name of group"
      },
      "id": "cluster id",
      "info": {
        "CertificateAuthorityData": "Y29udGFpbnMgUEVNLWVuY29kZWQgY2VydGlmaWNhdGUgYXV0aG9yaXR5IGNlcnRpZmljYXRlcw==",
        "server": "address of the kubernetes cluster (https://hostname:port)"
      },
      "issuer": "the identity issuer tied to this cluster",
      "last_seen": {
        "nanos": 320188840,
        "seconds": 1680015013
      },
      "managed_name": "unique name assigned to this cluster's managed agent",
      "name": "name of the cluster",
      "registered": {
        "nanos": 320188340,
        "seconds": 1680015013
      },
      "remote_id": "the remote ID of this cluster",
      "status": {
        "message": "message",
        "reason": "reason"
      },
      "version": "the Kubernetes version last reported by the cluster"
    },
    "parent_id": "Parent group under which this cluster resides"
  }
}
```
### Method: Delete

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/tenant/v1/clusters
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.32458443Z
Ce-Type: dev.chainguard.api.tenant.cluster.deleted.v1
Content-Length: 108
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "id": "id is the exact UIDP of the record"
  }
}
```
### Method: Update

#### Example HTTP Headers

```
POST / HTTP/1.1
Host: console-api.enforce.dev
Accept-Encoding: gzip
Authorization: Bearer oidctoken
Ce-Audience: customer
Ce-Group: UID of parent group
Ce-Id: cloudevent generated UUID
Ce-Source: https://console-api.enforce.dev/tenant/v1/clusters
Ce-Specversion: 1.0
Ce-Subject: UIDP identifier
Ce-Time: 2023-03-28T14:50:13.324786648Z
Ce-Type: dev.chainguard.api.tenant.cluster.updated.v1
Content-Length: 1666
Content-Type: application/json
User-Agent: Chainguard Enforce

```

#### Example HTTP Body

```json
{
  "actor": {
    "subject": "identity that triggered the event"
  },
  "body": {
    "activity": {
      "example activity": {
        "controller_name": "the name of the Controller CRD which was the source of this activity on the tenant cluster",
        "last_seen": {
          "nanos": 320190241,
          "seconds": 1680015013
        },
        "namespace": "the namespace in which the source of this cluster activity lives",
        "spec_hash": "the hash of the Controller or Webhook CRD's spec",
        "webhook_name": "the name of the Webhook CRD which was the source of this activity on the tenant cluster"
      }
    },
    "agent_version": "the version of the Chainguard agent last reported by the cluster",
    "auth_info": {
      "client_certificate_data": "Y29udGFpbnMgUEVNLWVuY29kZWQgZGF0YSBmcm9tIGEgY2xpZW50IGNlcnQgZmlsZSBmb3IgVExT",
      "client_key_data": "Y29udGFpbnMgUEVNLWVuY29kZWQgZGF0YSBmcm9tIGEgY2xpZW50IGtleSBmaWxlIGZvciBUTFM="
    },
    "description": "a short description of this cluster",
    "group": {
      "description": "human readable description of group",
      "id": "group UIDP under which this group resides",
      "name": "human readable name of group"
    },
    "id": "Cluster UID/GID under which this cluster resides",
    "info": {
      "CertificateAuthorityData": "Y29udGFpbnMgUEVNLWVuY29kZWQgY2VydGlmaWNhdGUgYXV0aG9yaXR5IGNlcnRpZmljYXRlcw==",
      "server": "address of the kubernetes cluster (https://hostname:port)"
    },
    "issuer": "the identity issuer tied to this cluster",
    "last_seen": {
      "nanos": 320190041,
      "seconds": 1680015013
    },
    "managed_name": "unique name assigned to this cluster's managed agent",
    "name": "name of the cluster",
    "registered": {
      "nanos": 320189841,
      "seconds": 1680015013
    },
    "remote_id": "the remote ID of this cluster",
    "status": {
      "message": "message",
      "reason": "reason"
    },
    "version": "the Kubernetes version last reported by the cluster"
  }
}
```
