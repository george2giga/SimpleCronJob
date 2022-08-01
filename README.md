# SimpleCronJob

An [IHostedService](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-6.0&tabs=visual-studio "IHostedService") implementation to easily add cron like jobs (scheduled jobs) within an ASP.NET Core web application.

## Usage
Install the nuget package:
```powershell
Install-Package SimpleCronJob -Version 1.0.2
```
Implement your cron job:
```csharp
public class MyCronJob : CronJobService
{
    private readonly ILogger<MyCronJob> _logger;

    public MyCronJob(IScheduleConfig<MyCronJob> config, ILogger<MyCronJob> logger)
        : base(config.CronExpression, config.TimeZoneInfo)
    {
        _logger = logger;
    }

    public override Task StartAsync(CancellationToken cancellationToken)
    {
        _logger.LogInformation("MyCronJob starts.");
        return base.StartAsync(cancellationToken);
    }

    public override Task DoWork(CancellationToken cancellationToken)
    {
        _logger.LogInformation($"{DateTime.Now:hh:mm:ss} MyCronJob is working.");
        // Add your logic here
        return Task.CompletedTask;
    }

    public override Task StopAsync(CancellationToken cancellationToken)
    {
        _logger.LogInformation("MyCronJob is stopping.");
        return base.StopAsync(cancellationToken);
    }
}
```

Register your cron job(s) at startup:

*The scheduling is set using Cron expression with https://github.com/HangfireIO/Cronos*

```csharp
using SimpleCronJob;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddCronJob<MyCronJob>(c =>
{
    c.TimeZoneInfo = TimeZoneInfo.Local;
    c.CronExpression = @"0 8 * * *"; // run every day at 8:00 AM
});

// add as many cron jobs as you need
builder.Services.AddCronJob<MyOtherCronJob>(c =>
{
    c.TimeZoneInfo = TimeZoneInfo.Local;
    c.CronExpression = @"0 9 * * *"; // run every day at 9:00 AM
});
```


## Credits

https://codeburst.io/schedule-cron-jobs-using-hostedservice-in-asp-net-core-e17c47ba06
