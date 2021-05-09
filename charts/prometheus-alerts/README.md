# Prometheus-Alerts chart

## Summary
This is a simple chart for managing Prometheus alerts over multiple clusters.
The main future of this chart is to be able to override specific parameters in the alerts using Helm's values override.

## Default recording-rules
There are also default recording rules (Based on Israel timezone) for setting alerts only during business hurs
```
      - record: is_israel_summer_time
        expr: |
          (vector(1) and (month() > 3 and month() < 10))
          or
          (vector(1) and (month() == 3 and (day_of_month() - day_of_week()) >= 25) and absent((day_of_month() >= 25) and (day_of_week() == 0)))
          or
          (vector(1) and (month() == 10 and (day_of_month() - day_of_week()) < 25) and absent((day_of_month() >= 25) and (day_of_week() == 0)))
          or
          (vector(1) and ((month() == 10 and hour() < 1) or (month() == 3 and hour() > 0)) and ((day_of_month() >= 25) and (day_of_week() == 0)))
          or
          vector(0)
      - record: israel_localtime
        expr: time() + 3600 + 3600 + 3600 * is_israel_summer_time
      - record: business_day
        expr: vector(1) and day_of_week(israel_localtime) >= 0 and day_of_week(israel_localtime) < 5
      - record: israel_hour
        expr: hour(israel_localtime)
      - record: business_hour
        expr: vector(1) and israel_hour >= 9 < 18 and business_day
```
