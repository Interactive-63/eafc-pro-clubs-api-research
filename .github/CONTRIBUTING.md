# Contributing

Contributions are welcome. This repository is intended to be a shared reference for EA FC Pro Clubs API research, and additional findings from independent research are encouraged.

You don't need to be an experienced developer to contribute. Identifying an event ID, documenting an endpoint or providing evidence for an existing finding is useful.

## Ways to contribute

You can contribute by:

* Identifying previously undocumented API endpoints.
* Documenting new response fields.
* Identifying the meaning of event IDs.
* Finding or validating player statistic formulas.
* Investigating differences between game releases.
* Correcting existing documentation.
* Improving examples and explanations.

## Before submitting

Please check the existing documentation and open issues to avoid duplicating existing research.

For new findings, include as much supporting evidence as possible. A single observation can be useful, but it should be clearly identified as an observation rather than a confirmed result.

Where possible, test proposed formulas against multiple matches and players, including examples where the relevant statistic is non-zero.

## Confidence levels

Please use the confidence levels already established in the repository.

* **Authoritative:** A directly available, named API field or established source of truth.
* **High:** Strongly validated by repeated observations, exact-match testing or independent confirmation.
* **Medium:** Supported by meaningful evidence but with incomplete validation or known exceptions.
* **Low:** A possible relationship supported by limited evidence.
* **Unconfirmed:** A hypothesis that has not yet been sufficiently tested.

Do not assign high confidence based on correlation alone.

## Submitting a finding

There are two ways to contribute.

### Issues

Use an issue to propose a finding, ask a question or share research that is not yet ready to be incorporated into the documentation.

Include the game version, relevant endpoint or event IDs, your observations and any supporting evidence.

### Pull requests

Use a pull request to propose changes to the documentation.

For new formulas or event mappings, include the evidence supporting the proposed relationship and explain any known exceptions.

Pull requests should make it clear which information is directly observed and which parts are interpretations or hypotheses.

## Research standards

A few things are particularly important for this project.

* Keep observations separate from interpretations.
* Don't assume that similarly named statistics use the same underlying events.
* Consider whether a formula is a complete partition, a partial breakdown or an overlapping category.
* Account for statistics that are almost always zero when validating relationships.
* Record the game version in which a finding was observed.
* Don't remove an existing finding simply because a newer observation contradicts it. Document the discrepancy so it can be investigated.

When a finding is contradicted by new evidence, the documentation should be updated to reflect the uncertainty rather than silently discarding the earlier result.

## Data and privacy

Do not submit account credentials, authentication tokens, personal information or other sensitive data.

Only include API responses and other research material that you are permitted to share publicly. Remove identifying information where appropriate.

## Review process

Contributions may be reviewed, tested or left open for further investigation before being merged.

Not every proposed finding will immediately be added to the confirmed documentation. Unverified discoveries may instead be recorded as exploratory research until sufficient evidence is available.

Thanks for helping improve the reference.
