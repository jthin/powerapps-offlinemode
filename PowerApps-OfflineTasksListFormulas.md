# PowerApps - Mode offline
Pricipales formules du projet PowerApps-OfflineTasksList.

## App - OnStart

``` 
    If(Connection.Connected, 
        
        //Online mode
        ClearCollect(LocalTasksList, TasksList);    
        ClearCollect(LocalTaskStatus, Choices(TasksList.Status)); //Dropdown status
        SaveData(LocalTasksList, "LocalTasksList");
        SaveData(LocalTaskStatus, "LocalTaskStatus"),
        
        //Offline mode
        LoadData(LocalTasksList, "LocalTasksList")
    );
```

## Ecran Browse > Timer (OnTimerEnd)
```
If(
    Connection.Connected && CountRows(TasksQueue) > 0,
    // Mise à jour
    ForAll(
        TasksQueue,
		If(
            TaskAction = "edit", //Update a task
            Patch(
                TasksList,
                First(Filter(TasksList, ID = TaskID)),
                {
                    Title: TaskTitle,
                    Description: TaskDescription,
                    Status: {Value: TaskStatus}
                }
            )
        );
        If(
            TaskAction = "add", //Create a new task
            Patch(
                TasksList,
                Defaults(TasksList),
                {
                    Title: TaskTitle,
                    Description: TaskDescription,
                    Status: {Value: TaskStatus}
                }
            )
        );
		If(
            TaskAction = "delete", //Delete a task
            Remove(
                TasksList,
                First(Filter(TasksList, ID = TaskID))
            )
        );
    );
    //Clear TasksQueue (PowerApps cache)
    Clear(TasksQueue);
    SaveData(TasksQueue, "TasksQueue");
    //Update PowerApps cache with last data 
    Refresh(TasksList);
    ClearCollect(LocalTasksList, TasksList);
    SaveData(LocalTasksList, "TasksList");
)
```

## CRUD

### Create or update
```
If(
    Connection.Connected,

    //Online mode
    If(
        EditForm1.Mode = New,
    
        //Create a new task
        Patch(
            TasksList,
            Defaults(TasksList),
            {
                Title: DataCardValue4.Text,
                Description: DataCardValue5.Text,
                Status: {Value: DataCardValue6.Selected.Value}
            }
        ),
        Patch(
            TasksList,
            First(
                Filter(
                    TasksList,
                    ID = BrowseGallery1.Selected.ID
                )
            ),
            {
                Title: DataCardValue4.Text,
                Description: DataCardValue5.Text,
                Status: {Value: DataCardValue6.Selected.Value}
            }
        )
    );
    
    //Update PowerApps cache
    Clear(TasksQueue);
    SaveData(
        TasksQueue,
        "TasksQueue"
    );
    Refresh(TasksList);
    ClearCollect(
        LocalTasksList,
        TasksList
    );
    SaveData(
        LocalTasksList,
        "TasksList"
    ),
    
    //Edit an existing task
    //Offline mode
    If(
        EditForm1.Mode = New,
        //Create a new task
        Collect(
            TasksQueue,
            {
                TaskID: BrowseGallery1.Selected.ID,
                TaskTitle: DataCardValue4.Text,
                TaskDescription: DataCardValue5.Text,
                TaskStatus: DataCardValue6.Selected.Value,
                TaskAction: "add"
            }
        ),
        //Edit an existing task
        Collect(
            TasksQueue,
            {
                TaskID: BrowseGallery1.Selected.ID,
                TaskTitle: DataCardValue4.Text,
                TaskDescription: DataCardValue5.Text,
                TaskStatus: DataCardValue6.Selected.Value,
                TaskAction: "edit"
            }
        )
    )
);
```

### Delete
```
If(
    Connection.Connected,
    
    //Online mode
    Remove(
        TasksList,
        First(
            Filter(
                TasksList,
                ID = BrowseGallery1.Selected.ID
            )
        )
    );
    Refresh(TasksList);
    ClearCollect(
        LocalTasksList,
        TasksList
    );
    SaveData(
        LocalTasksList,
        "TasksList"
    ),
    
    //Device is not connected, store temporary data in PowerApps cache
    Collect(
        TasksQueue,
        {
            TaskID: BrowseGallery1.Selected.ID,
            TaskTitle: DataCardValue4.Text,
            TaskDescription: DataCardValue5.Text,
            TaskStatus: DataCardValue6.Selected.Value,
            TaskAction: "delete"
        }
    )
);
```