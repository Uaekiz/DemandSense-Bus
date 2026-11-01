# DemandSense Bus

Intent-based public transport demand estimation.

SWE 305 — Software Project Management  
Sakarya University

## Project Overview

DemandSense Bus is a mobile prototype designed to estimate
public transport demand from user interactions with bus lines
and stops.

The system compares current interaction scores with historical
reference values and displays relative demand as:

- Quieter than usual
- Normal
- Busier than usual

The indicators represent estimated interest, not measured
passenger counts or actual vehicle occupancy.

## Planned Features

- User authentication
- Pilot bus line and stop listings
- Interaction-based demand scoring
- Historical baseline comparison
- Relative demand indicators
- Synthetic data generation
- A small admin and test panel
- Automated tests and setup documentation

## Scoring Rules

- Viewing a stop for at least 3 seconds: +1 point
- Opening direct bus or line tracking: +3 points
- Repeated interactions by the same user for the same line
  or stop are not counted again within 15 minutes

## Project Documentation

- [Project Charter](docs/DemandSense_Project_Charter.pdf)
- [Project Plan Word](docs/DemandSense_Project_Plan_EN.docx)

The project plan contains the WBS, proposed responsibilities,
Gantt chart, activity network, critical path analysis,
cost estimates and risk mitigation plan.

## Team

| Member | Role |
|--------|------|
| Umut Arda Ekiz | Project Manager |
| Faruk Turnalı | Team Member |
| Ali Caner Sezer | Team Member |
| Ahmet Hilmi Güler | Team Member |
| Hüseyin Yılmaz | Team Member |
| Yusuf Büyüktaş | Team Member |
| Muhammet Ali Damgacı | Team Member |

## Project Status

Planning stage.

The implementation is planned for eight weeks.
Detailed assignments and technical choices in the project
plan are proposals for team review.
