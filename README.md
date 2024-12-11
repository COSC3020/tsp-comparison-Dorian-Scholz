# Traveling Salesperson Problem -- Empirical Analysis

For this exercise, you'll need to take the code from the TSP Held-Karp and TSP
Local Search exercises. This can be your own implementation or somebody else's.
You will now do an empirical analysis of the implementations, comparing their
performance. Both the Held-Karp and the Local Search algorithms solve the same
problem, but they do so in completely different ways. This results in different
solutions, and in different times required to get to the solution.

Investigate the implementations' empirical time complexity, i.e. how the runtime
increases as the input size increases. *Measure* this time by running the code
instead of reasoning from the asymptotic complexity (this is the empirical
part). Create inputs of different sizes and plot how the runtime scales (input
size on the $x$ axis, time on the $y$ axis). Your largest input should have a
runtime of *at least* an hour. The input size that gets you to an hour will
probably not be the same for the Held-Karp and Local Search implementations.

In addition to the measured runtime, plot the tour lengths obtained by both
implementations on the same input distance matrices. The length of the tour that
Held-Karp found should always be less than or equal to the tour length that
Local Search found. Why is this?

Add the code to run your experiments, graphs, and an explanation of what you did
to this markdown file.

Code:
Here’s a function to generate matrices:
function generateDistanceMatrix(size)
{
    let matrix = Array.from({ length: size }, () =>
        Array.from({ length: size }, () => Math.floor(Math.random() * 100) + 1)
    );
    for (let i = 0; i < size; i++) 
    {
        matrix[i][i] = 0; // Distance from a city to itself is 0
        for (let j = 0; j < i; j++) 
        {
            matrix[i][j] = matrix[j][i]; 
        }
    }
    return matrix;
}

Runtime function
function measureRuntime(func, distanceMatrix) {
    const start = performance.now();
    const result = func(distanceMatrix);
    const end = performance.now();
    return { runtime: end - start, result };
}

---------------------------------------------------------------------------------
const sizes = [4, 6, 8, 10, 12]; // Gradually increase size
const results = [];

for (const size of sizes) 
{
    const matrix = generateDistanceMatrix(size);

    console.log(`Testing size: ${size}`);

    const hk = measureRuntime(tsp_hk, matrix);
    const ls = measureRuntime(tsp_ls, matrix);

    results.push(
    {
        size,
        hkRuntime: hk.runtime,
        hkTourLength: hk.result,
        lsRuntime: ls.runtime,
        lsTourLength: ls.result,
    });

    console.log(`Held-Karp: Time = ${hk.runtime}ms, Tour Length = ${hk.result}`);
    console.log(`Local Search: Time = ${ls.runtime}ms, Tour Length = ${ls.result}`);
}
--------------------------------------------------------------------------------------
This should Plot the results:
<canvas id="runtimeChart"></canvas>
<canvas id="tourLengthChart"></canvas>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
    const runtimeData = {
        labels: [4, 6, 8, 10, 12], // Input sizes
        datasets: [
            {
                label: 'Held-Karp Runtime',
                data: results.map(r => r.hkRuntime),
                borderColor: 'red',
                fill: false,
            },
            {
                label: 'Local Search Runtime',
                data: results.map(r => r.lsRuntime),
                borderColor: 'blue',
                fill: false,
            },
        ],
    };

    const tourLengthData = {
        labels: [4, 6, 8, 10, 12], // Input sizes
        datasets: [
            {
                label: 'Held-Karp Tour Length',
                data: results.map(r => r.hkTourLength),
                borderColor: 'red',
                fill: false,
            },
            {
                label: 'Local Search Tour Length',
                data: results.map(r => r.lsTourLength),
                borderColor: 'blue',
                fill: false,
            },
        ],
    };

    new Chart(document.getElementById('runtimeChart'), {
        type: 'line',
        data: runtimeData,
    });

    new Chart(document.getElementById('tourLengthChart'), {
        type: 'line',
        data: tourLengthData,
    });
</script>
