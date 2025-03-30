## Metric filter
### Counts number of  [INFO} in cloud watch log
```
resource "aws_cloudwatch_log_metric_filter" "info_count" {
  name           = "info-count"
  pattern        = "[INFO]"
  log_group_name = aws_cloudwatch_log_group.http_api.name # Lambda function name

  metric_transformation {
    name          = "info-count"
    namespace     = "/moviedb-api/${var.chrisy_yumyum}" # Uses the alias variable
    value         = "1"
    default_value = "0"
  }
}
```

## Alarm trigger at detecting 3 [INFO] in cloud watch log within 60 seconds
```
resource "aws_cloudwatch_metric_alarm" "info_log_count_alarm" {
  alarm_name          = "${local.name_prefix}-info-log-count"
  alarm_description   = "Alarm when INFO log count exceeds 3 in 1 minute"
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = 1
  threshold           = 3
  period              = 60 # 1 minute in seconds
  statistic           = "Sum"
  treat_missing_data  = "notBreaching" # How to handle missing data points

  metric_name = "info-count"                        # Must match the metric name in your filter
  namespace   = "/moviedb-api/${var.chrisy_yumyum}" # Must match your metric namespace
```


## Alarm action 
### Send email to a subscribed email address
```
resource "aws_sns_topic_subscription" "email_subscription" {
  topic_arn = aws_sns_topic.chrisy_alert_topic.arn
  protocol  = "email"
  endpoint  = "chrisyeohc@outlook.com"
}
```

### Action when alarm triggered
```
alarm_actions = [
      aws_sns_topic.chrisy_alert_topic.arn
  ]
```

### Action When alarm turned off
```
ok_actions = [
        aws_sns_topic.chrisy_alert_topic.arn
  ]
```

### Future modifications
Customize the email alert message with personalized messages and information
