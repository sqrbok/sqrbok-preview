---
title: Domain-Specific V&V
parent: Quality Planning
nav_order: 7
layout: default
---

# Domain-Specific V&V

**Domain-specific V&V** adapts the generic verification and validation activities of a quality plan to a domain's own hazards, regulations and technology: robots that act in an unpredictable world, Internet-of-Things systems built from heterogeneous devices, cars whose software comes from suppliers, and systems designed as executable models {% cite araujo2022ras minani2024iot arcanjo2023automotive cederbladh2023vv %}. Four systematic reviews show what changes.

| Domain | Review | What dominates V&V |
|---|---|---|
| Robotic and autonomous systems | 195 primary studies | formal verification and simulation; model checkers such as Prism and Uppaal, simulators such as Gazebo |
| Internet of Things | 83 primary studies from 8,294 candidates | many quality attributes at once, led by interoperability and performance |
| Automotive embedded software | 62 primary studies from 408 | the V-model and staged in-the-loop testing, split between supplier and car maker |
| Model-based systems engineering | 149 primary studies from 701 | checking models before anything is built: simulation, model checking, reviews |

## 1. Robotic and autonomous systems

Araujo et al. found that testing and verifying robotic and autonomous systems needs more than conventional software testing: environments are uncertain, behaviour depends on physical dynamics, and the input space is vast. The most widely used tools are model checkers (Prism, Uppaal) and simulators (Gazebo, Matlab/Simulink). Evidence of industrial use is very limited: only 10% of the proposed techniques were evaluated in an industrial setting, and many papers lack any rigorous measure of efficiency or adequacy {% cite araujo2022ras %}.

## 2. Internet of Things

Minani et al. identified 47 quality attributes that drive IoT test objectives. Interoperability and performance were the most frequent (each in 17 studies, 20.4%), followed by scalability (13 studies, 15.6%) and usability (10, 12.0%); the shares overlap because one study can address several attributes {% cite minani2024iot %}. A plan for an IoT system therefore needs tests across device types and protocols, not just functional tests of one device.

## 3. Automotive embedded software

Arcanjo et al. confirm the V-model as the dominant life cycle. Responsibility is split: the car maker (OEM) owns requirements, system design and the high-level tests such as system integration and in-vehicle validation; the supplier owns the lower design levels and the low-level tests such as model and software integration tests {% cite arcanjo2023automotive %}. Testing advances through stages: model-in-the-loop, software-in-the-loop, hardware-in-the-loop and vehicle-in-the-loop. A recurring conclusion is that ISO 26262 is not ideal for autonomous vehicles, whose requirements cannot be fully defined when test cases are first written {% cite arcanjo2023automotive %}.

## 4. Model-based systems engineering

In model-based systems engineering, the models are the main engineering artefacts. Cederbladh et al. reviewed how system behaviour is verified and validated early, on the models: simulation, model checking, manual review, static analysis and model transformations {% cite cederbladh2023vv %}. A quality plan for such a project names the models as work products and puts gates on them before implementation starts.

## Example: one plan, two products

A company builds a parking-assist function for cars and a companion phone app. For the car function, the plan follows the V-model, assigns model- and software-in-the-loop tests to the supplier and vehicle tests to the car maker, and sets criticality by the automotive safety level. For the app, the plan follows the team's two-week iterations, with tests across phone models and network conditions. The same eleven decisions, two very different answers. _(Constructed example.)_

## When to use it, and when not

- **Use a domain's review** as a checklist of risks and techniques when the plan's first decision (activities) must fit a regulated or physical domain.
- **Do not import** a heavy domain process into a product that does not share the domain's hazards.

## Limitations

- **Reviews describe research, not practice.** The robotics review in particular finds little industrial evaluation.
- **Domains move fast.** Standards for autonomous systems are still being written.

## How solid is this?

- **Where it comes from.** Four systematic literature reviews: Araujo et al., Minani et al., Arcanjo et al., Cederbladh et al. {% cite araujo2022ras minani2024iot arcanjo2023automotive cederbladh2023vv %}.
- **What is contested.** Whether ISO 26262 can be extended to autonomous driving or needs a new standard.
- **What we do not hold.** The standards themselves (ISO 26262, DO-178C).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
