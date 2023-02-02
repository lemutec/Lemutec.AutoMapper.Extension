# AutoMapper.Lemutec.Extension

## Intro

Single file AutoMapper Extension.

Copy the `MapperExtension.cs` file to your project.

## Usage

> Create yourself MapperProvider

```c#
public static class MapperProvider
{
    public static IMapper Service { get; }

    static MapperProvider()
    {
        MapperConfiguration configuration = new(CreateMap);
#if DEBUG
        configuration.AssertConfigurationIsValid();
#endif
        Service = configuration.CreateMapper();
    }

    private static void CreateMap(IMapperConfigurationExpression cfg)
    {
        cfg.CreateMap<MyProperty, MyPropertyViewModel>().IgnoreAllMembersNull().IgnoreAllNotMappedAttribute().Forget();

        cfg.CreateMap<MyPropertyViewModel, MyProperty>().IgnoreAllMembersNull().IgnoreAllNotMappedAttribute().Forget();

        cfg.CreateMap<PointAngle, PointProperty>().ForAllMembersCustom((src, dest) =>
        {
            // My mapper func here.
        });

        cfg.CreateMap<MyPropertyViewModel, MyPropertyViewModel>().IgnoreAllMembersNull().ForAllMembersCloneable().Forget();
    }

    public static TDestination MapDefault<TSource, TDestination>(TSource source, TDestination destination)
    {
        return Map(source, destination, cfg =>
        {
            cfg.CreateMap<TSource, TDestination>();
        });
    }

    public static TDestination MapDefault<TSource, TDestination>(TSource source)
    {
        return Map<TSource, TDestination>(source, cfg =>
        {
            cfg.CreateMap<TSource, TDestination>();
        });
    }

    public static TDestination Map<TSource, TDestination>(TSource source, TDestination destination, Action<IMapperConfigurationExpression> configure = null!)
    {
        if (configure != null)
        {
            MapperConfiguration configuration = new(configure);
            IMapper mapper = configuration.CreateMapper();
            return mapper.Map(source, destination);
        }
        if (Service?.ConfigurationProvider.FindTypeMapFor<TSource, TDestination>() != null)
        {
            return Service.Map(source, destination);
        }
        else
        {
            return MapDefault(source, destination);
        }
    }

    public static TDestination Map<TSource, TDestination>(TSource source, Action<IMapperConfigurationExpression> configure = null!)
    {
        if (configure != null)
        {
            MapperConfiguration configuration = new(configure);
            IMapper mapper = configuration.CreateMapper();
            return mapper.Map<TSource, TDestination>(source);
        }
        if (Service?.ConfigurationProvider.FindTypeMapFor<TSource, TDestination>() != null)
        {
            return Service.Map<TSource, TDestination>(source);
        }
        else
        {
            return MapDefault<TSource, TDestination>(source);
        }
    }
}
```

