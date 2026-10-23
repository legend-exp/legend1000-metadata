# Background model inputs

`<experiment>/components.yaml` holds what is needed to scale the PDFs of the
Simflow simulations: the quantity of each component of the experiment and its
radioactivity.

## Format

The file maps a component name to an entry with these fields:

- `description` (str): name of the component.
- `reference` (str): where the values come from.
- one quantity field, together with the matching activity field:

  | quantity           | activity                          |
  | ------------------ | --------------------------------- |
  | `mass_in_kg`       | `specific_activity_in_uBq_per_kg` |
  | `area_in_m2`       | `specific_activity_in_uBq_per_m2` |
  | `number_of_pieces` | `activity_per_piece_in_uBq`       |

The activity field maps a `simid` of `tier/stp/<experiment>/simconfig.yaml` to
the activity of the decays simulated in it, written as either:

- `{ val, unc }`: measured value and its uncertainty (one standard deviation).
- `{ limit, cl }`: upper limit and its confidence level (e.g. `0.9`).

```yaml
neutron_moderator:
  description: Neutron Moderator (PMMA)
  reference: LEGEND-1000 CDR v2.1, Table XVII
  mass_in_kg: 7000
  specific_activity_in_uBq_per_kg:
    neutron_moderator_Pb214_to_Po214: { val: 16, unc: 6 }
    neutron_moderator_Pb212_to_Pb208: { limit: 28, cl: 0.9 }
```

A simulation that stands for several components (e.g. the HV clamps, for the
Ultem and phosphor bronze parts of the HV connectors) is listed under each of
them.
