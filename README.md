# Asset-Lifecycle
import React, { useState, useEffect, useMemo } from 'react';
import { LineChart, Line, BarChart, Bar, PieChart, Pie, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer, Cell } from 'recharts';

// --- Mock Data ---
// NOTE: mockUnits and mockBuildings are now populated with the full list provided by the user.
// Other mock data remains the same.

const mockYearlyData = [
  { year: '2021', balance: 52636, contribution: 52636, expenditure: 0 }, { year: '2022', balance: 106324, contribution: 52899, expenditure: 0 }, { year: '2023', balance: 161082, contribution: 53163, expenditure: 0 }, { year: '2024', balance: 216927, contribution: 53429, expenditure: 0 }, { year: '2025', balance: 273878, contribution: 53696, expenditure: 0 }, { year: '2026', balance: 331951, contribution: 53965, expenditure: 0 }, { year: '2027', balance: 391164, contribution: 54235, expenditure: 0 }, { year: '2028', balance: 451538, contribution: 54506, expenditure: 0 }, { year: '2029', balance: 513089, contribution: 54778, expenditure: 0 }, { year: '2030', balance: 559944, contribution: 55052, expenditure: 15893 }, { year: '2031', balance: 619783, contribution: 55327, expenditure: 3888 }, { year: '2032', balance: 541103, contribution: 55604, expenditure: 143581 }, { year: '2033', balance: 452039, contribution: 55882, expenditure: 153062 }, { year: '2034', balance: 509909, contribution: 56161, expenditure: 5072 }, { year: '2035', balance: 305725, contribution: 56442, expenditure: 268275 }, { year: '2036', balance: 254388, contribution: 56725, expenditure: 112647 }, { year: '2037', balance: 285738, contribution: 57008, expenditure: 29474 }, { year: '2038', balance: 125242, contribution: 57293, expenditure: 222075 }, { year: '2039', balance: 184700, contribution: 57580, expenditure: 0 }, { year: '2040', balance: 34961, contribution: 57868, expenditure: 210377 }
];
const mockZoneBalances = [
  { name: 'Master Community', value: 798352, color: '#4E4456' }, { name: 'Typical Buildings', value: 227018, color: '#6D5D7B' }, { name: 'Zone 3 (Al Zaha)', value: 63604, color: '#8F7C9B' }, { name: 'Zone 5 (Al Nameer)', value: 63581, color: '#AD9BBD' }, { name: 'Zone 8 (Al Wajd)', value: 37884, color: '#CBB9DB' }, { name: 'Staff Accommodation', value: 273878, color: '#E9D7F5' }
];
const mockAssetCategories = [
  { name: 'Infrastructure', value: 2000000, color: '#4E4456' }, { name: 'MEP Systems', value: 1500000, color: '#6D5D7B' }, { name: 'Finishes/Structure', value: 500000, color: '#8F7C9B' }, { name: 'Landscaping', value: 500000, color: '#CBB9DB' }
];
const mockUpcomingReplacements = [
  { component: 'Helipad Electrical Works', location: 'Master Community', year: 2025, cost: 10920 }, { component: 'Fire Extinguishers', location: 'Typical Buildings', year: 2026, cost: 129 }, { component: 'Lagoon Infrastructure', location: 'Master Community', year: 2027, cost: 42000 }, { component: 'Elevator Wire Ropes', location: 'Typical Buildings', year: 2027, cost: 2450 }, { component: 'External Wall Paint', location: 'Typical Buildings', year: 2028, cost: 1465 }, { component: 'Tree Uplighters', location: 'Zone 3', year: 2031, cost: 1120 }
];
const mockZones = [
  { id: '3', name: 'Zone 3 (Al Zaha)', propertyTypes: ['Apartment', 'Villa'] }, { id: '5', name: 'Zone 5 (Al Nameer)', propertyTypes: ['Villa'] }, { id: '8', name: 'Zone 8 (Al Wajd)', propertyTypes: ['Villa'] }, { id: 'staff', name: 'Staff Accommodation', propertyTypes: ['Staff Accommodation'] }
];

// **UPDATED** Mock data defining buildings within zones/property types
const mockBuildings = {
  '3': {
    'Apartment': [ // Extracted from Unit Nos like Z3 0XX(...)
      'D44', 'D45', 'D46', 'D47', 'D48', 'D49', 'D50', 'D51', 'D52', 'D53',
      'D54', 'D55', 'D56', 'D57', 'D58', 'D59', 'D60', 'D61', 'D62', 'D74', 'D75'
    ]
  },
  'staff': { // Added Staff Accommodation buildings
      'Staff Accommodation': ['B1', 'B2', 'B3', 'B4', 'B5', 'B6', 'B7', 'B8', 'CIF']
  }
};

// **UPDATED** Mock data defining units, populated from the user's table
const mockUnits = {
  '3': { // Zone 3
    'Villa': [
      { id: 'Z3-001', unitNo: 'Z3 001', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-002', unitNo: 'Z3 002', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-003', unitNo: 'Z3 003', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-004', unitNo: 'Z3 004', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-005', unitNo: 'Z3 005', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-006', unitNo: 'Z3 006', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-007', unitNo: 'Z3 007', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-008', unitNo: 'Z3 008', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-009', unitNo: 'Z3 009', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-010', unitNo: 'Z3 010', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-011', unitNo: 'Z3 011', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-012', unitNo: 'Z3 012', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-013', unitNo: 'Z3 013', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-014', unitNo: 'Z3 014', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-015', unitNo: 'Z3 015', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-016', unitNo: 'Z3 016', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-017', unitNo: 'Z3 017', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-018', unitNo: 'Z3 018', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-019', unitNo: 'Z3 019', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-020', unitNo: 'Z3 020', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-021', unitNo: 'Z3 021', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-022', unitNo: 'Z3 022', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-023', unitNo: 'Z3 023', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-024', unitNo: 'Z3 024', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-025', unitNo: 'Z3 025', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-026', unitNo: 'Z3 026', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-027', unitNo: 'Z3 027', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-028', unitNo: 'Z3 028', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-029', unitNo: 'Z3 029', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-030', unitNo: 'Z3 030', type: '3 Bedroom Zaha Villa', bua: 357 },
      { id: 'Z3-031', unitNo: 'Z3 031', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-032', unitNo: 'Z3 032', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-033', unitNo: 'Z3 033', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-034', unitNo: 'Z3 034', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-035', unitNo: 'Z3 035', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-036', unitNo: 'Z3 036', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-037', unitNo: 'Z3 037', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-038', unitNo: 'Z3 038', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-039', unitNo: 'Z3 039', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-040', unitNo: 'Z3 040', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-041', unitNo: 'Z3 041', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-042', unitNo: 'Z3 042', type: '4 Bedroom Zaha Villa', bua: 422 },
      { id: 'Z3-043', unitNo: 'Z3 043', type: '4 Bedroom Zaha Villa', bua: 422 },
    ],
    'Apartment': {
      'D44': [
        { id: 'Z3-044-1', unitNo: 'Z3 044(1)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-044-2', unitNo: 'Z3 044(2)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-044-3', unitNo: 'Z3 044(3)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-044-4', unitNo: 'Z3 044(4)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-044-5', unitNo: 'Z3 044(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-044-6', unitNo: 'Z3 044(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D45': [
        { id: 'Z3-045-1', unitNo: 'Z3 045(1)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-045-2', unitNo: 'Z3 045(2)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-045-3', unitNo: 'Z3 045(3)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-045-4', unitNo: 'Z3 045(4)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-045-5', unitNo: 'Z3 045(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-045-6', unitNo: 'Z3 045(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D46': [
        { id: 'Z3-046-1', unitNo: 'Z3 046(1)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-046-2', unitNo: 'Z3 046(2)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-046-3', unitNo: 'Z3 046(3)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-046-4', unitNo: 'Z3 046(4)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-046-5', unitNo: 'Z3 046(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-046-6', unitNo: 'Z3 046(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D47': [
         { id: 'Z3-047-1', unitNo: 'Z3 047(1)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-047-2', unitNo: 'Z3 047(2)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-047-3', unitNo: 'Z3 047(3)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-047-4', unitNo: 'Z3 047(4)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-047-5', unitNo: 'Z3 047(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
         { id: 'Z3-047-6', unitNo: 'Z3 047(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
       'D48': [
         { id: 'Z3-048-1', unitNo: 'Z3 048(1)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-048-2', unitNo: 'Z3 048(2)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-048-3', unitNo: 'Z3 048(3)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-048-4', unitNo: 'Z3 048(4)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-048-5', unitNo: 'Z3 048(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
         { id: 'Z3-048-6', unitNo: 'Z3 048(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
       'D49': [
         { id: 'Z3-049-1', unitNo: 'Z3 049(1)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-049-2', unitNo: 'Z3 049(2)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-049-3', unitNo: 'Z3 049(3)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-049-4', unitNo: 'Z3 049(4)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-049-5', unitNo: 'Z3 049(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
         { id: 'Z3-049-6', unitNo: 'Z3 049(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
       'D50': [
         { id: 'Z3-050-1', unitNo: 'Z3 050(1)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-050-2', unitNo: 'Z3 050(2)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-050-3', unitNo: 'Z3 050(3)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-050-4', unitNo: 'Z3 050(4)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-050-5', unitNo: 'Z3 050(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
         { id: 'Z3-050-6', unitNo: 'Z3 050(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
       'D51': [
         { id: 'Z3-051-1', unitNo: 'Z3 051(1)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-051-2', unitNo: 'Z3 051(2)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-051-3', unitNo: 'Z3 051(3)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-051-4', unitNo: 'Z3 051(4)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-051-5', unitNo: 'Z3 051(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
         { id: 'Z3-051-6', unitNo: 'Z3 051(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
       'D52': [
         { id: 'Z3-052-1', unitNo: 'Z3 052(1)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-052-2', unitNo: 'Z3 052(2)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-052-3', unitNo: 'Z3 052(3)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-052-4', unitNo: 'Z3 052(4)', type: '2 Bedroom Premium Apartment', bua: 199 },
         { id: 'Z3-052-5', unitNo: 'Z3 052(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
         { id: 'Z3-052-6', unitNo: 'Z3 052(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
       'D53': [
        { id: 'Z3-053-1A', unitNo: 'Z3 053(1A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-053-1B', unitNo: 'Z3 053(1B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-053-2A', unitNo: 'Z3 053(2A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-053-2B', unitNo: 'Z3 053(2B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-053-3A', unitNo: 'Z3 053(3A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-053-3B', unitNo: 'Z3 053(3B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-053-4A', unitNo: 'Z3 053(4A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-053-4B', unitNo: 'Z3 053(4B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-053-5', unitNo: 'Z3 053(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-053-6', unitNo: 'Z3 053(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D54': [
        { id: 'Z3-054-1A', unitNo: 'Z3 054(1A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-054-1B', unitNo: 'Z3 054(1B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-054-2A', unitNo: 'Z3 054(2A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-054-2B', unitNo: 'Z3 054(2B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-054-3A', unitNo: 'Z3 054(3A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-054-3B', unitNo: 'Z3 054(3B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-054-4A', unitNo: 'Z3 054(4A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-054-4B', unitNo: 'Z3 054(4B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-054-5', unitNo: 'Z3 054(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-054-6', unitNo: 'Z3 054(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D55': [
        { id: 'Z3-055-1A', unitNo: 'Z3 055(1A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-055-1B', unitNo: 'Z3 055(1B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-055-2A', unitNo: 'Z3 055(2A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-055-2B', unitNo: 'Z3 055(2B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-055-3A', unitNo: 'Z3 055(3A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-055-3B', unitNo: 'Z3 055(3B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-055-4A', unitNo: 'Z3 055(4A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-055-4B', unitNo: 'Z3 055(4B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-055-5', unitNo: 'Z3 055(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-055-6', unitNo: 'Z3 055(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D56': [
        { id: 'Z3-056-1A', unitNo: 'Z3 056(1A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-056-1B', unitNo: 'Z3 056(1B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-056-2A', unitNo: 'Z3 056(2A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-056-2B', unitNo: 'Z3 056(2B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-056-3A', unitNo: 'Z3 056(3A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-056-3B', unitNo: 'Z3 056(3B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-056-4A', unitNo: 'Z3 056(4A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-056-4B', unitNo: 'Z3 056(4B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-056-5', unitNo: 'Z3 056(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-056-6', unitNo: 'Z3 056(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D57': [
        { id: 'Z3-057-1A', unitNo: 'Z3 057(1A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-057-1B', unitNo: 'Z3 057(1B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-057-2A', unitNo: 'Z3 057(2A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-057-2B', unitNo: 'Z3 057(2B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-057-3A', unitNo: 'Z3 057(3A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-057-3B', unitNo: 'Z3 057(3B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-057-4A', unitNo: 'Z3 057(4A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-057-4B', unitNo: 'Z3 057(4B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-057-5', unitNo: 'Z3 057(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-057-6', unitNo: 'Z3 057(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D58': [
        { id: 'Z3-058-1A', unitNo: 'Z3 058(1A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-058-1B', unitNo: 'Z3 058(1B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-058-2A', unitNo: 'Z3 058(2A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-058-2B', unitNo: 'Z3 058(2B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-058-3A', unitNo: 'Z3 058(3A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-058-3B', unitNo: 'Z3 058(3B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-058-4A', unitNo: 'Z3 058(4A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-058-4B', unitNo: 'Z3 058(4B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-058-5', unitNo: 'Z3 058(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-058-6', unitNo: 'Z3 058(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D59': [
        { id: 'Z3-059-1A', unitNo: 'Z3 059(1A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-059-1B', unitNo: 'Z3 059(1B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-059-2A', unitNo: 'Z3 059(2A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-059-2B', unitNo: 'Z3 059(2B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-059-3A', unitNo: 'Z3 059(3A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-059-3B', unitNo: 'Z3 059(3B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-059-4A', unitNo: 'Z3 059(4A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-059-4B', unitNo: 'Z3 059(4B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-059-5', unitNo: 'Z3 059(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-059-6', unitNo: 'Z3 059(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D60': [
        { id: 'Z3-060-1A', unitNo: 'Z3 060(1A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-060-1B', unitNo: 'Z3 060(1B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-060-2A', unitNo: 'Z3 060(2A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-060-2B', unitNo: 'Z3 060(2B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-060-3A', unitNo: 'Z3 060(3A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-060-3B', unitNo: 'Z3 060(3B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-060-4A', unitNo: 'Z3 060(4A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-060-4B', unitNo: 'Z3 060(4B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-060-5', unitNo: 'Z3 060(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-060-6', unitNo: 'Z3 060(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D61': [
        { id: 'Z3-061-1A', unitNo: 'Z3 061(1A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-061-1B', unitNo: 'Z3 061(1B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-061-2A', unitNo: 'Z3 061(2A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-061-2B', unitNo: 'Z3 061(2B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-061-3A', unitNo: 'Z3 061(3A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-061-3B', unitNo: 'Z3 061(3B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-061-4A', unitNo: 'Z3 061(4A)', type: '2 Bedroom Small Apartment', bua: 115 },
        { id: 'Z3-061-4B', unitNo: 'Z3 061(4B)', type: '1 Bedroom Apartment', bua: 79 },
        { id: 'Z3-061-5', unitNo: 'Z3 061(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-061-6', unitNo: 'Z3 061(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D62': [
        { id: 'Z3-062-1', unitNo: 'Z3 062(1)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-062-2', unitNo: 'Z3 062(2)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-062-3', unitNo: 'Z3 062(3)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-062-4', unitNo: 'Z3 062(4)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-062-5', unitNo: 'Z3 062(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-062-6', unitNo: 'Z3 062(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D74': [
        { id: 'Z3-074-1', unitNo: 'Z3 074(1)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-074-2', unitNo: 'Z3 074(2)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-074-3', unitNo: 'Z3 074(3)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-074-4', unitNo: 'Z3 074(4)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-074-5', unitNo: 'Z3 074(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-074-6', unitNo: 'Z3 074(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
      'D75': [
        { id: 'Z3-075-1', unitNo: 'Z3 075(1)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-075-2', unitNo: 'Z3 075(2)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-075-3', unitNo: 'Z3 075(3)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-075-4', unitNo: 'Z3 075(4)', type: '2 Bedroom Premium Apartment', bua: 199 },
        { id: 'Z3-075-5', unitNo: 'Z3 075(5)', type: '3 Bedroom Zaha Apartment', bua: 355 },
        { id: 'Z3-075-6', unitNo: 'Z3 075(6)', type: '3 Bedroom Zaha Apartment', bua: 361 },
      ],
    }
  },
  '5': { // Zone 5
    'Villa': [
      { id: 'Z5-001', unitNo: 'Z5 001', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-002', unitNo: 'Z5 002', type: '3 Bedroom Nameer Villa', bua: 427 },
      { id: 'Z5-003', unitNo: 'Z5 003', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-004', unitNo: 'Z5 004', type: '3 Bedroom Nameer Villa', bua: 427 },
      { id: 'Z5-005', unitNo: 'Z5 005', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-006', unitNo: 'Z5 006', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-007', unitNo: 'Z5 007', type: '3 Bedroom Nameer Villa', bua: 427 },
      { id: 'Z5-008', unitNo: 'Z5 008', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-009', unitNo: 'Z5 009', type: '3 Bedroom Nameer Villa', bua: 427 },
      { id: 'Z5-010', unitNo: 'Z5 010', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-011', unitNo: 'Z5 011', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-012', unitNo: 'Z5 012', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-013', unitNo: 'Z5 013', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-014', unitNo: 'Z5 014', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-015', unitNo: 'Z5 015', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-016', unitNo: 'Z5 016', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-017', unitNo: 'Z5 017', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-018', unitNo: 'Z5 018', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-019', unitNo: 'Z5 019', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-020', unitNo: 'Z5 020', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-021', unitNo: 'Z5 021', type: '3 Bedroom Nameer Villa', bua: 427 },
      { id: 'Z5-022', unitNo: 'Z5 022', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-023', unitNo: 'Z5 023', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-024', unitNo: 'Z5 024', type: '3 Bedroom Nameer Villa', bua: 427 },
      { id: 'Z5-025', unitNo: 'Z5 025', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-026', unitNo: 'Z5 026', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-027', unitNo: 'Z5 027', type: '3 Bedroom Nameer Villa', bua: 427 },
      { id: 'Z5-028', unitNo: 'Z5 028', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-029', unitNo: 'Z5 029', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-030', unitNo: 'Z5 030', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-031', unitNo: 'Z5 031', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-032', unitNo: 'Z5 032', type: '4 Bedroom Nameer Villa', bua: 498 },
      { id: 'Z5-033', unitNo: 'Z5 033', type: '4 Bedroom Nameer Villa', bua: 498 },
    ]
  },
  '8': { // Zone 8
    'Villa': [
      { id: 'Z8-001', unitNo: 'Z8 001', type: '5 Bedroom Wajd Villa', bua: 750 },
      { id: 'Z8-002', unitNo: 'Z8 002', type: '5 Bedroom Wajd Villa', bua: 750 },
      { id: 'Z8-003', unitNo: 'Z8 003', type: '5 Bedroom Wajd Villa', bua: 750 },
      { id: 'Z8-004', unitNo: 'Z8 004', type: '5 Bedroom Wajd Villa', bua: 750 },
      { id: 'Z8-005', unitNo: 'Z8 005', type: '5 Bedroom Wajd Villa', bua: 943 },
      { id: 'Z8-006', unitNo: 'Z8 006', type: '5 Bedroom Wajd Villa', bua: 760 },
      { id: 'Z8-007', unitNo: 'Z8 007', type: '5 Bedroom Wajd Villa', bua: 750 },
      { id: 'Z8-008', unitNo: 'Z8 008', type: '5 Bedroom Wajd Villa', bua: 760 },
      { id: 'Z8-009', unitNo: 'Z8 009', type: '5 Bedroom Wajd Villa', bua: 1187 },
      { id: 'Z8-010', unitNo: 'Z8 010', type: '5 Bedroom Wajd Villa', bua: 760 },
      { id: 'Z8-011', unitNo: 'Z8 011', type: '5 Bedroom Wajd Villa', bua: 750 },
      { id: 'Z8-012', unitNo: 'Z8 012', type: '5 Bedroom Wajd Villa', bua: 760 },
      { id: 'Z8-013', unitNo: 'Z8 013', type: '5 Bedroom Wajd Villa', bua: 760 },
      { id: 'Z8-014', unitNo: 'Z8 014', type: '5 Bedroom Wajd Villa', bua: 760 },
      { id: 'Z8-015', unitNo: 'Z8 015', type: '5 Bedroom Wajd Villa', bua: 760 },
      { id: 'Z8-016', unitNo: 'Z8 016', type: '5 Bedroom Wajd Villa', bua: 760 },
      { id: 'Z8-017', unitNo: 'Z8 017', type: '5 Bedroom Wajd Villa', bua: 750 },
      { id: 'Z8-018', unitNo: 'Z8 018', type: '5 Bedroom Wajd Villa', bua: 760 },
      { id: 'Z8-019', unitNo: 'Z8 019', type: '5 Bedroom Wajd Villa', bua: 750 },
      { id: 'Z8-020', unitNo: 'Z8 020', type: '5 Bedroom Wajd Villa', bua: 760 },
      { id: 'Z8-021', unitNo: 'Z8 021', type: '5 Bedroom Wajd Villa', bua: 750 },
      { id: 'Z8-022', unitNo: 'Z8 022', type: 'King Villa', bua: 1845 },
    ]
  },
  'staff': { // Staff Accommodation (Zone 1 implied)
      'Staff Accommodation': { // Property Type
          // Added building keys but unit arrays are empty as details were not provided
          'B1': [],
          'B2': [],
          'B3': [],
          'B4': [],
          'B5': [],
          'B6': [],
          'B7': [],
          'B8': [],
          'CIF': []
      }
  }
};

// Base rates for 2025 (OMR per sq.m.)
const rates2025 = {
  masterCommunity: 1.75, // AED rate, used here as OMR equivalent
  typicalBuilding: 1.65,
  zone3: 0.44,
  zone5: 1.10,
  zone8: 0.33,
  staffAccommodation: 3.95
};

// --- Custom Transition Component ---
// (Transition component remains the same - omitted for brevity)
const Transition = ({ show, enter, enterFrom, enterTo, leave, leaveFrom, leaveTo, children, className, unmount = true }) => {
  const [shouldRender, setShouldRender] = useState(show);
  const [transitionState, setTransitionState] = useState(show ? 'enterTo' : 'leaveTo');
  useEffect(() => { let timeoutId; if (show) { setShouldRender(true); requestAnimationFrame(() => { setTransitionState('enterFrom'); requestAnimationFrame(() => { setTransitionState('enterTo'); }); }); } else { setTransitionState('leaveFrom'); timeoutId = setTimeout(() => { setTransitionState('leaveTo'); if (unmount) { setShouldRender(false); } }, 300); } return () => clearTimeout(timeoutId); }, [show, unmount]);
  if (!shouldRender) { return null; }
  let currentTransitionClasses = '';
  switch (transitionState) { case 'enterFrom': currentTransitionClasses = `${enter} ${enterFrom}`; break; case 'enterTo': currentTransitionClasses = `${enter} ${enterTo}`; break; case 'leaveFrom': currentTransitionClasses = `${leave} ${leaveFrom}`; break; case 'leaveTo': currentTransitionClasses = `${leave} ${leaveTo}`; break; default: break; }
  return ( <div className={`${className || ''} ${currentTransitionClasses}`}> {children} </div> );
};


// --- Main App Component ---
// (App component structure remains the same - omitted for brevity)
const App = () => {
  const [activeTab, setActiveTab] = useState('dashboard');
  const [menuOpen, setMenuOpen] = useState(false);
  const [showSettings, setShowSettings] = useState(false);
  const [darkMode, setDarkMode] = useState(false);
  const [compactView, setCompactView] = useState(false);
  useEffect(() => { document.documentElement.classList.toggle('dark', darkMode); }, [darkMode]);
  return ( <div className={`min-h-screen ${darkMode ? 'bg-gray-900 text-gray-100' : 'bg-gray-50 text-gray-800'} flex flex-col transition-colors duration-300`}> <header className={`bg-gradient-to-r from-[#4E4456] to-[#6D5D7B] text-white shadow-lg sticky top-0 z-10 transition-all duration-300 ${compactView ? 'py-1' : 'py-3'}`}> <div className="container mx-auto px-4"> <div className="flex justify-between items-center"> <div className="flex items-center space-x-2 flex-shrink-0"> <div className="h-9 w-9 rounded-full bg-white/20 flex items-center justify-center"> <svg xmlns="http://www.w3.org/2000/svg" className="h-6 w-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2"> <path d="M3 6a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2v12a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V6z" /> <path d="M3 6h18v4H3z" /> <path d="M9 14h6" /> <path d="M9 18h6" /> </svg> </div> <h1 className={`font-bold tracking-tight transition-all duration-300 ${compactView ? 'text-lg' : 'text-xl'}`}>Muscat Bay Reserve Fund</h1> </div> <nav className="hidden md:flex space-x-1 flex-grow justify-center px-4"> {['dashboard', 'calculator', 'reports', 'assets'].map((tab) => ( <button key={tab} className={`px-4 py-2 text-sm font-medium rounded-md transition-all duration-200 whitespace-nowrap ${ activeTab === tab ? 'bg-white/20 text-white' : 'text-white/80 hover:bg-white/10 hover:text-white' }`} onClick={() => setActiveTab(tab)}>{tab.charAt(0).toUpperCase() + tab.slice(1)}</button> ))} </nav> <div className="flex items-center space-x-2 flex-shrink-0"> <button className="md:flex hidden items-center p-2 rounded-md text-white hover:bg-white/20 transition-colors" onClick={() => setShowSettings(!showSettings)} aria-label="Settings"> <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"> <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" /> <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" /> </svg> </button> <button className="md:hidden p-2 rounded-md text-white hover:bg-white/20 transition-colors" onClick={() => setMenuOpen(!menuOpen)} aria-label="Open Menu"> <svg xmlns="http://www.w3.org/2000/svg" className="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"> <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M4 6h16M4 12h16M4 18h16" /> </svg> </button> </div> </div> <Transition show={menuOpen} enter="transition ease-out duration-200" enterFrom="transform opacity-0 scale-95" enterTo="transform opacity-100 scale-100" leave="transition ease-in duration-150" leaveFrom="transform opacity-100 scale-100" leaveTo="transform opacity-0 scale-95"> <div className="md:hidden px-2 pt-2 pb-3 space-y-1 bg-[#4E4456]/90 backdrop-blur-sm"> {['dashboard', 'calculator', 'reports', 'assets'].map((tab) => ( <button key={tab} className={`block w-full text-left px-3 py-2 text-base font-medium rounded-md transition-all duration-200 ${ activeTab === tab ? 'bg-white/20 text-white' : 'text-white/70 hover:bg-white/10 hover:text-white' }`} onClick={() => { setActiveTab(tab); setMenuOpen(false); }}>{tab.charAt(0).toUpperCase() + tab.slice(1)}</button> ))} <button className="block w-full text-left px-3 py-2 text-base font-medium rounded-md text-white/70 hover:bg-white/10 hover:text-white transition-all duration-200" onClick={() => { setShowSettings(!showSettings); setMenuOpen(false); }}>Settings</button> </div> </Transition> <Transition show={showSettings} enter="transition ease-out duration-200" enterFrom="transform opacity-0 -translate-y-4" enterTo="transform opacity-100 translate-y-0" leave="transition ease-in duration-150" leaveFrom="transform opacity-100 translate-y-0" leaveTo="transform opacity-0 -translate-y-4" className="absolute right-4 top-16 z-20" unmount={false}> <div className={`rounded-lg shadow-xl p-4 ${darkMode ? 'bg-gray-800' : 'bg-white'} w-64`}> <h3 className="font-medium mb-3">Settings</h3> <div className="space-y-3"> <div className="flex items-center justify-between"><span className="text-sm">Dark Mode</span><button onClick={() => setDarkMode(!darkMode)} className={`relative inline-flex items-center h-6 rounded-full w-11 transition-colors focus:outline-none ${darkMode ? 'bg-[#6D5D7B]' : 'bg-gray-300'}`} aria-pressed={darkMode}><span className={`inline-block w-4 h-4 transform transition-transform bg-white rounded-full ${darkMode ? 'translate-x-6' : 'translate-x-1'}`} /></button></div> <div className="flex items-center justify-between"><span className="text-sm">Compact View</span><button onClick={() => setCompactView(!compactView)} className={`relative inline-flex items-center h-6 rounded-full w-11 transition-colors focus:outline-none ${compactView ? 'bg-[#6D5D7B]' : 'bg-gray-300'}`} aria-pressed={compactView}><span className={`inline-block w-4 h-4 transform transition-transform bg-white rounded-full ${compactView ? 'translate-x-6' : 'translate-x-1'}`} /></button></div> <div className="pt-2 border-t border-gray-200 dark:border-gray-700"><button className="text-sm text-[#4E4456] dark:text-[#AD9BBD] hover:text-[#6D5D7B] dark:hover:text-[#E9D7F5] transition-colors" onClick={() => setShowSettings(false)}>Close Settings</button></div> </div> </div> </Transition> </div> </header> <main className={`flex-1 container mx-auto px-4 transition-all duration-300 ${compactView ? 'py-3' : 'py-6'} relative`}> <Transition show={activeTab === 'dashboard'} unmount={false} className={`transition-opacity duration-300 ${activeTab !== 'dashboard' ? 'absolute top-0 left-0 right-0 opacity-0 pointer-events-none' : 'opacity-100'}`} enter="ease-out" enterFrom="opacity-0" enterTo="opacity-100" leave="ease-in" leaveFrom="opacity-100" leaveTo="opacity-0"> <Dashboard compactView={compactView} darkMode={darkMode} /> </Transition> <Transition show={activeTab === 'calculator'} unmount={false} className={`transition-opacity duration-300 ${activeTab !== 'calculator' ? 'absolute top-0 left-0 right-0 opacity-0 pointer-events-none' : 'opacity-100'}`} enter="ease-out" enterFrom="opacity-0" enterTo="opacity-100" leave="ease-in" leaveFrom="opacity-100" leaveTo="opacity-0"> <Calculator compactView={compactView} darkMode={darkMode} /> </Transition> <Transition show={activeTab === 'reports'} unmount={false} className={`transition-opacity duration-300 ${activeTab !== 'reports' ? 'absolute top-0 left-0 right-0 opacity-0 pointer-events-none' : 'opacity-100'}`} enter="ease-out" enterFrom="opacity-0" enterTo="opacity-100" leave="ease-in" leaveFrom="opacity-100" leaveTo="opacity-0"> <div className='text-center py-20'> <h2 className={`text-2xl font-bold ${darkMode ? 'text-gray-300' : 'text-gray-500'}`}>Reports Module</h2> <p className={`mt-2 ${darkMode ? 'text-gray-400' : 'text-gray-500'}`}>This module will be available in the next update.</p> <button className="mt-4 px-4 py-2 bg-gradient-to-r from-[#4E4456] to-[#6D5D7B] text-white rounded-md hover:opacity-90 transition-opacity" onClick={() => setActiveTab('dashboard')}>Return to Dashboard</button> </div> </Transition> <Transition show={activeTab === 'assets'} unmount={false} className={`transition-opacity duration-300 ${activeTab !== 'assets' ? 'absolute top-0 left-0 right-0 opacity-0 pointer-events-none' : 'opacity-100'}`} enter="ease-out" enterFrom="opacity-0" enterTo="opacity-100" leave="ease-in" leaveFrom="opacity-100" leaveTo="opacity-0"> <div className='text-center py-20'> <h2 className={`text-2xl font-bold ${darkMode ? 'text-gray-300' : 'text-gray-500'}`}>Asset Management</h2> <p className={`mt-2 ${darkMode ? 'text-gray-400' : 'text-gray-500'}`}>This module will be available in the next update.</p> <button className="mt-4 px-4 py-2 bg-gradient-to-r from-[#4E4456] to-[#6D5D7B] text-white rounded-md hover:opacity-90 transition-opacity" onClick={() => setActiveTab('dashboard')}>Return to Dashboard</button> </div> </Transition> </main> <footer className={`${darkMode ? 'bg-gray-800 border-gray-700' : 'bg-gray-100 border-gray-200'} border-t py-4 transition-colors duration-300`}> <div className="container mx-auto px-4 text-center text-sm text-gray-500 dark:text-gray-400"> <p>© {new Date().getFullYear()} Muscat Bay Reserve Fund Management</p> <p className="text-xs mt-1">Version 1.0.3</p> </div> </footer> <GlobalStyles /> </div> );
};


// --- Dashboard Component ---
// (Dashboard component remains the same - omitted for brevity)
const Dashboard = ({ compactView, darkMode }) => {
  const [selectedYear, setSelectedYear] = useState('2025'); const [yearsToShow, setYearsToShow] = useState(10); const [selectedChart, setSelectedChart] = useState('balance');
  const filteredYearlyData = useMemo(() => mockYearlyData.slice(0, yearsToShow), [yearsToShow]);
  const currentYearData = useMemo(() => mockYearlyData.find(data => data.year === selectedYear) || mockYearlyData[4], [selectedYear]); const fundingPercentage = 72;
  const criticalComponents = useMemo(() => mockUpcomingReplacements.filter(item => parseInt(item.year) <= parseInt(selectedYear) + 2 ).length, [selectedYear]);
  const chartColors = { balance: '#4E4456', contribution: '#6D5D7B', expenditure: '#AD9BBD' };
  return ( <div className="space-y-6"> <div className="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4"> <h2 className={`${compactView ? 'text-xl' : 'text-2xl'} font-bold text-[#4E4456] dark:text-[#AD9BBD]`}> Reserve Fund Dashboard </h2> <div className="flex flex-wrap items-center gap-4"> <div className="flex items-center gap-2"> <label className={`text-sm font-medium ${darkMode ? 'text-gray-300' : 'text-gray-600'}`}>Year:</label> <select className={`border rounded-md px-3 py-1 text-sm focus:outline-none focus:ring-2 focus:ring-[#4E4456]/50 ${ darkMode ? 'bg-gray-700 border-gray-600 text-white' : 'border-gray-300' }`} value={selectedYear} onChange={(e) => setSelectedYear(e.target.value)}> {mockYearlyData.map(data => ( <option key={data.year} value={data.year}>{data.year}</option> ))} </select> </div> <div className="flex items-center gap-2"> <label className={`text-sm font-medium ${darkMode ? 'text-gray-300' : 'text-gray-600'}`}>Forecast:</label> <select className={`border rounded-md px-3 py-1 text-sm focus:outline-none focus:ring-2 focus:ring-[#4E4456]/50 ${ darkMode ? 'bg-gray-700 border-gray-600 text-white' : 'border-gray-300' }`} value={yearsToShow} onChange={(e) => setYearsToShow(parseInt(e.target.value))}> <option value={5}>5 Years</option> <option value={10}>10 Years</option> <option value={20}>20 Years</option> </select> </div> </div> </div> <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4"> <KpiCard title="Total Reserve Balance" value={`OMR ${currentYearData.balance.toLocaleString()}`} description="Projected EOY Balance" trend="up" icon={ <svg className="w-7 h-7" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"> <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" /> </svg> } compactView={compactView} darkMode={darkMode} /> <KpiCard title="Annual Contribution" value={`OMR ${currentYearData.contribution.toLocaleString()}`} description="Projected for the year" trend="up" icon={ <svg className="w-7 h-7" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"> <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 11l3-3m0 0l3 3m-3-3v8m0-13a9 9 0 110 18 9 9 0 010-18z" /> </svg> } compactView={compactView} darkMode={darkMode} /> <KpiCard title="Funding Percentage" value={`${fundingPercentage}%`} description="Current funding adequacy" trend={fundingPercentage >= 70 ? "up" : "down"} icon={ <svg className="w-7 h-7" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"> <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M16 8v8m-4-5v5m-4-2v2m-2 4h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z" /> </svg> } compactView={compactView} darkMode={darkMode} /> <KpiCard title="Critical Components" value={criticalComponents.toString()} description="Requiring replacement soon" trend={criticalComponents <= 5 ? "up" : "down"} icon={ <svg className="w-7 h-7" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"> <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" /> </svg> } compactView={compactView} darkMode={darkMode} /> </div> <div className="grid grid-cols-1 lg:grid-cols-2 gap-6"> <ChartCard title="Reserve Fund Overview" darkMode={darkMode}> <div className="flex justify-center mb-4"> <div className="inline-flex rounded-md border border-gray-200 dark:border-gray-700 p-1"> {['balance', 'contribution', 'expenditure'].map((chart) => ( <button key={chart} className={`px-3 py-1 text-sm font-medium rounded transition-colors ${ selectedChart === chart ? 'bg-[#4E4456] text-white' : `${darkMode ? 'text-gray-300 hover:bg-gray-700' : 'text-gray-700 hover:bg-gray-100'}` }`} onClick={() => setSelectedChart(chart)}> {chart.charAt(0).toUpperCase() + chart.slice(1)} </button> ))} </div> </div> <ResponsiveContainer width="100%" height={300}> {selectedChart === 'expenditure' ? ( <BarChart data={filteredYearlyData}> <CartesianGrid strokeDasharray="3 3" stroke={darkMode ? '#374151' : '#E5E7EB'} /> <XAxis dataKey="year" tick={{ fill: darkMode ? '#9CA3AF' : '#4B5563' }} /> <YAxis tickFormatter={(value) => `${value / 1000}k`} tick={{ fill: darkMode ? '#9CA3AF' : '#4B5563' }} /> <Tooltip formatter={(value) => [`OMR ${value.toLocaleString()}`, 'Expenditure']} labelFormatter={(label) => `Year: ${label}`} contentStyle={{ backgroundColor: darkMode ? '#1F2937' : '#FFFFFF', borderColor: darkMode ? '#374151' : '#E5E7EB', borderRadius: '0.5rem' }} itemStyle={{ color: darkMode ? '#E5E7EB' : '#1F2937' }} labelStyle={{ color: darkMode ? '#E5E7EB' : '#1F2937' }} /> <Legend /> <Bar dataKey="expenditure" name="Expenditure" fill="#AD9BBD" radius={[4, 4, 0, 0]}> {filteredYearlyData.map((entry, index) => ( <Cell key={`cell-${index}`} fill={entry.expenditure > 100000 ? '#4E4456' : '#AD9BBD'} /> ))} </Bar> </BarChart> ) : ( <LineChart data={filteredYearlyData}> <CartesianGrid strokeDasharray="3 3" stroke={darkMode ? '#374151' : '#E5E7EB'} /> <XAxis dataKey="year" tick={{ fill: darkMode ? '#9CA3AF' : '#4B5563' }} /> <YAxis tickFormatter={(value) => `${value / 1000}k`} tick={{ fill: darkMode ? '#9CA3AF' : '#4B5563' }} /> <Tooltip formatter={(value, name) => [`OMR ${value.toLocaleString()}`, name === 'balance' ? 'Balance' : 'Contribution']} labelFormatter={(label) => `Year: ${label}`} contentStyle={{ backgroundColor: darkMode ? '#1F2937' : '#FFFFFF', borderColor: darkMode ? '#374151' : '#E5E7EB', borderRadius: '0.5rem' }} itemStyle={{ color: darkMode ? '#E5E7EB' : '#1F2937' }} labelStyle={{ color: darkMode ? '#E5E7EB' : '#1F2937' }} /> <Legend /> <Line type="monotone" dataKey={selectedChart} name={selectedChart === 'balance' ? 'Reserve Balance' : 'Annual Contribution'} stroke={chartColors[selectedChart]} strokeWidth={2} dot={{ r: 4, strokeWidth: 2 }} activeDot={{ r: 6, strokeWidth: 2 }} /> </LineChart> )} </ResponsiveContainer> </ChartCard> <ChartCard title="Fund Distribution by Zone (EOY 2025)" darkMode={darkMode}> <div className="flex flex-col md:flex-row items-center justify-center"> <div className="w-full md:w-1/2"> <ResponsiveContainer width="100%" height={260}> <PieChart> <Pie data={mockZoneBalances} cx="50%" cy="50%" labelLine={false} outerRadius={80} fill="#8884d8" dataKey="value" nameKey="name" label={({ name, percent }) => `${(percent * 100).toFixed(0)}%`} > {mockZoneBalances.map((entry, index) => ( <Cell key={`cell-${index}`} fill={entry.color} /> ))} </Pie> <Tooltip formatter={(value) => `OMR ${value.toLocaleString()}`} contentStyle={{ backgroundColor: darkMode ? '#1F2937' : '#FFFFFF', borderColor: darkMode ? '#374151' : '#E5E7EB', borderRadius: '0.5rem' }} itemStyle={{ color: darkMode ? '#E5E7EB' : '#1F2937' }} labelStyle={{ color: darkMode ? '#E5E7EB' : '#1F2937' }} /> </PieChart> </ResponsiveContainer> </div> <div className="w-full md:w-1/2 mt-4 md:mt-0"> <div className="space-y-2"> {mockZoneBalances.map((zone, index) => ( <div key={index} className="flex items-center justify-between"> <div className="flex items-center"> <div className="w-3 h-3 rounded-full mr-2" style={{ backgroundColor: zone.color }} ></div> <span className={`text-sm ${darkMode ? 'text-gray-300' : 'text-gray-600'}`}>{zone.name}</span> </div> <span className={`text-sm font-medium ${darkMode ? 'text-white' : 'text-gray-800'}`}> OMR {zone.value.toLocaleString()} </span> </div> ))} </div> </div> </div> </ChartCard> <ChartCard title="Asset Categories by Replacement Value" darkMode={darkMode}> <div className="flex flex-col md:flex-row items-center justify-center"> <div className="w-full md:w-1/2"> <ResponsiveContainer width="100%" height={260}> <PieChart> <Pie data={mockAssetCategories} cx="50%" cy="50%" labelLine={false} outerRadius={80} fill="#8884d8" dataKey="value" nameKey="name" label={({ percent }) => `${(percent * 100).toFixed(0)}%`} > {mockAssetCategories.map((entry, index) => ( <Cell key={`cell-${index}`} fill={entry.color} /> ))} </Pie> <Tooltip formatter={(value) => `OMR ${value.toLocaleString()}`} contentStyle={{ backgroundColor: darkMode ? '#1F2937' : '#FFFFFF', borderColor: darkMode ? '#374151' : '#E5E7EB', borderRadius: '0.5rem' }} itemStyle={{ color: darkMode ? '#E5E7EB' : '#1F2937' }} labelStyle={{ color: darkMode ? '#E5E7EB' : '#1F2937' }} /> </PieChart> </ResponsiveContainer> </div> <div className="w-full md:w-1/2 mt-4 md:mt-0"> <div className="space-y-2"> {mockAssetCategories.map((category, index) => ( <div key={index} className="flex items-center justify-between"> <div className="flex items-center"> <div className="w-3 h-3 rounded-full mr-2" style={{ backgroundColor: category.color }} ></div> <span className={`text-sm ${darkMode ? 'text-gray-300' : 'text-gray-600'}`}>{category.name}</span> </div> <span className={`text-sm font-medium ${darkMode ? 'text-white' : 'text-gray-800'}`}> OMR {(category.value / 1000000).toFixed(1)}M </span> </div> ))} </div> </div> </div> </ChartCard> <ChartCard title="Upcoming Component Replacements" darkMode={darkMode}> <div className="overflow-x-auto"> <table className={`min-w-full divide-y ${darkMode ? 'divide-gray-700' : 'divide-gray-200'}`}> <thead className={darkMode ? 'bg-gray-800' : 'bg-gray-50'}> <tr> <th scope="col" className={`px-6 py-3 text-left text-xs font-medium ${darkMode ? 'text-gray-400' : 'text-gray-500'} uppercase tracking-wider`}>Component</th> <th scope="col" className={`px-6 py-3 text-left text-xs font-medium ${darkMode ? 'text-gray-400' : 'text-gray-500'} uppercase tracking-wider`}>Location</th> <th scope="col" className={`px-6 py-3 text-left text-xs font-medium ${darkMode ? 'text-gray-400' : 'text-gray-500'} uppercase tracking-wider`}>Est. Replace Year</th> <th scope="col" className={`px-6 py-3 text-right text-xs font-medium ${darkMode ? 'text-gray-400' : 'text-gray-500'} uppercase tracking-wider`}>Est. Cost (OMR)</th> <th scope="col" className={`px-6 py-3 text-left text-xs font-medium ${darkMode ? 'text-gray-400' : 'text-gray-500'} uppercase tracking-wider`}>Status</th> </tr> </thead> <tbody className={`${darkMode ? 'bg-gray-900 divide-y divide-gray-700' : 'bg-white divide-y divide-gray-200'}`}> {mockUpcomingReplacements .sort((a, b) => a.year - b.year) .map((replacement, index) => ( <tr key={index} className={`transition-colors ${darkMode ? 'hover:bg-gray-800' : 'hover:bg-gray-50'}`}> <td className="px-6 py-4 whitespace-nowrap"> <div className={`text-sm font-medium ${darkMode ? 'text-white' : 'text-gray-900'}`}>{replacement.component}</div> </td> <td className="px-6 py-4 whitespace-nowrap"> <div className={`text-sm ${darkMode ? 'text-gray-400' : 'text-gray-500'}`}>{replacement.location}</div> </td> <td className="px-6 py-4 whitespace-nowrap"> <div className={`text-sm ${darkMode ? 'text-gray-400' : 'text-gray-500'}`}>{replacement.year}</div> </td> <td className="px-6 py-4 whitespace-nowrap text-right"> <div className={`text-sm font-medium ${darkMode ? 'text-white' : 'text-gray-900'}`}>{replacement.cost.toLocaleString()}</div> </td> <td className="px-6 py-4 whitespace-nowrap"> <span className={`px-2 py-1 inline-flex text-xs leading-5 font-semibold rounded-full ${ replacement.year <= parseInt(selectedYear) + 1 ? 'bg-red-100 text-red-800 dark:bg-red-900 dark:text-red-200' : replacement.year <= parseInt(selectedYear) + 3 ? 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:text-yellow-200' : 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-200' }`}> {replacement.year <= parseInt(selectedYear) + 1 ? 'Critical' : replacement.year <= parseInt(selectedYear) + 3 ? 'Warning' : 'Good'} </span> </td> </tr> ))} </tbody> </table> </div> <div className="mt-4 flex justify-end"> <button className="px-4 py-2 text-sm bg-gradient-to-r from-[#4E4456] to-[#6D5D7B] text-white rounded-md hover:opacity-90 transition-opacity" > View All Components </button> </div> </ChartCard> </div> </div> );
};

// --- Calculator Component ---
// (Calculator component remains the same - omitted for brevity)
const Calculator = ({ compactView, darkMode }) => {
  const [selectedZone, setSelectedZone] = useState(''); const [selectedPropertyType, setSelectedPropertyType] = useState(''); const [selectedBuilding, setSelectedBuilding] = useState(''); const [selectedUnit, setSelectedUnit] = useState(''); const [calculationResult, setCalculationResult] = useState(null); const [loading, setLoading] = useState(false); const [selectedYear, setSelectedYear] = useState('2025');
  const propertyTypes = useMemo(() => selectedZone ? mockZones.find(z => z.id === selectedZone)?.propertyTypes || [] : [], [selectedZone]);
  const buildings = useMemo(() => (selectedZone && selectedPropertyType === 'Apartment' && mockBuildings[selectedZone]?.[selectedPropertyType]) || [], [selectedZone, selectedPropertyType]);
  const units = useMemo(() => { if (!selectedZone || !selectedPropertyType) return []; if (selectedPropertyType === 'Apartment') { if (!selectedBuilding && buildings.length > 0) return []; return mockUnits[selectedZone]?.[selectedPropertyType]?.[selectedBuilding] || []; } else { return mockUnits[selectedZone]?.[selectedPropertyType] || []; } }, [selectedZone, selectedPropertyType, selectedBuilding, buildings]);
  const getInflationAdjustedRates = (year) => { const yearDiff = parseInt(year) - 2025; const inflationRate = 0.005; const adjustmentFactor = Math.pow(1 + inflationRate, yearDiff); return { masterCommunity: rates2025.masterCommunity * adjustmentFactor, typicalBuilding: rates2025.typicalBuilding * adjustmentFactor, zone3: rates2025.zone3 * adjustmentFactor, zone5: rates2025.zone5 * adjustmentFactor, zone8: rates2025.zone8 * adjustmentFactor, staffAccommodation: rates2025.staffAccommodation * adjustmentFactor }; };
  const getUnitDetails = () => { if (!selectedZone || !selectedPropertyType || !selectedUnit) return null; if (selectedPropertyType === 'Apartment' && selectedBuilding) { return mockUnits[selectedZone]?.[selectedPropertyType]?.[selectedBuilding]?.find( unit => unit.id === selectedUnit ); } else { return mockUnits[selectedZone]?.[selectedPropertyType]?.find( unit => unit.id === selectedUnit ); } };
  const calculateContribution = () => { setLoading(true); setCalculationResult(null); setTimeout(() => { const unitDetails = getUnitDetails(); if (!unitDetails) { setLoading(false); return; } const adjustedRates = getInflationAdjustedRates(selectedYear); let masterContribution = 0; let zoneContribution = 0; let buildingContribution = 0; if (selectedZone !== 'staff') { masterContribution = unitDetails.bua * adjustedRates.masterCommunity; } switch (selectedZone) { case '3': zoneContribution = unitDetails.bua * adjustedRates.zone3; break; case '5': zoneContribution = unitDetails.bua * adjustedRates.zone5; break; case '8': zoneContribution = unitDetails.bua * adjustedRates.zone8; break; case 'staff': zoneContribution = unitDetails.bua * adjustedRates.staffAccommodation; break; default: zoneContribution = 0; } if (selectedPropertyType === 'Apartment') { buildingContribution = unitDetails.bua * adjustedRates.typicalBuilding; } const totalAnnualContribution = masterContribution + zoneContribution + buildingContribution; const componentBreakdown = []; if (masterContribution > 0) { componentBreakdown.push({ category: 'Master Community', components: [ { name: 'Roads & Infrastructure', share: masterContribution * 0.4 }, { name: 'Landscaping', share: masterContribution * 0.2 }, { name: 'Utilities', share: masterContribution * 0.3 }, { name: 'Other', share: masterContribution * 0.1 } ] }); } if (zoneContribution > 0) { componentBreakdown.push({ category: selectedZone === 'staff' ? 'Staff Accommodation' : `Zone ${selectedZone} Specific`, components: [ { name: `Zone ${selectedZone} Infrastructure`, share: zoneContribution * 0.5 }, { name: `Zone ${selectedZone} Amenities`, share: zoneContribution * 0.5 } ] }); } if (buildingContribution > 0) { componentBreakdown.push({ category: 'Building Specific', components: [ { name: 'Elevators', share: buildingContribution * 0.3 }, { name: 'Common HVAC', share: buildingContribution * 0.3 }, { name: 'Finishes', share: buildingContribution * 0.2 }, { name: 'Other', share: buildingContribution * 0.2 } ] }); } setCalculationResult({ unitDetails, masterContribution, zoneContribution, buildingContribution, totalAnnualContribution, componentBreakdown, year: selectedYear }); setLoading(false); }, 800); };
  const resetCalculator = () => { setSelectedZone(''); setSelectedPropertyType(''); setSelectedBuilding(''); setSelectedUnit(''); setSelectedYear('2025'); setCalculationResult(null); };
  const handleZoneChange = (e) => { setSelectedZone(e.target.value); setSelectedPropertyType(''); setSelectedBuilding(''); setSelectedUnit(''); setCalculationResult(null); }; const handlePropertyTypeChange = (e) => { setSelectedPropertyType(e.target.value); setSelectedBuilding(''); setSelectedUnit(''); setCalculationResult(null); }; const handleBuildingChange = (e) => { setSelectedBuilding(e.target.value); setSelectedUnit(''); setCalculationResult(null); }; const handleUnitChange = (e) => { setSelectedUnit(e.target.value); setCalculationResult(null); }; const handleYearChange = (e) => { setSelectedYear(e.target.value); setCalculationResult(null); }; const generatePDFReport = () => { alert('PDF Report generation feature is planned for a future update.'); };
  return ( <div className="space-y-6"> <div className="flex justify-between items-center"> <h2 className={`${compactView ? 'text-xl' : 'text-2xl'} font-bold text-[#4E4456] dark:text-[#AD9BBD]`}> Reserve Fund Calculator </h2> <button onClick={resetCalculator} className={`px-4 py-2 text-sm font-medium rounded-md border transition-colors ${ darkMode ? 'text-[#AD9BBD] border-[#AD9BBD] hover:bg-[#4E4456] hover:text-white' : 'text-[#4E4456] border-[#4E4456] hover:bg-[#4E4456] hover:text-white' }`}> Reset </button> </div> <div className="grid grid-cols-1 lg:grid-cols-2 gap-6"> <div className={`rounded-lg shadow-md p-6 ${darkMode ? 'bg-gray-800' : 'bg-white'}`}> <h3 className={`text-lg font-medium mb-4 ${darkMode ? 'text-[#AD9BBD]' : 'text-[#4E4456]'}`}>Property Selection</h3> <div className="space-y-4"> <div> <label className={`block text-sm font-medium mb-1 ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>Calculation Year</label> <select value={selectedYear} onChange={handleYearChange} className={`w-full border rounded-md px-3 py-2 focus:outline-none focus:ring-2 focus:ring-[#4E4456]/50 ${darkMode ? 'bg-gray-700 border-gray-600 text-white focus:border-[#6D5D7B]' : 'border-gray-300 focus:border-[#4E4456]'}`}> {mockYearlyData.filter(d => parseInt(d.year) >= 2025).map((data) => ( <option key={data.year} value={data.year}> {data.year}{data.year === '2025' ? ' (Base Year)' : ''} </option> ))} </select> </div> <div> <label className={`block text-sm font-medium mb-1 ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>Zone</label> <select value={selectedZone} onChange={handleZoneChange} className={`w-full border rounded-md px-3 py-2 focus:outline-none focus:ring-2 focus:ring-[#4E4456]/50 ${darkMode ? 'bg-gray-700 border-gray-600 text-white focus:border-[#6D5D7B]' : 'border-gray-300 focus:border-[#4E4456]'}`}> <option value="">Select Zone</option> {mockZones.map((zone) => (<option key={zone.id} value={zone.id}>{zone.name}</option>))} </select> </div> <div> <label className={`block text-sm font-medium mb-1 ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>Property Type</label> <select value={selectedPropertyType} onChange={handlePropertyTypeChange} disabled={!selectedZone} className={`w-full border rounded-md px-3 py-2 focus:outline-none focus:ring-2 focus:ring-[#4E4456]/50 ${darkMode ? 'bg-gray-700 border-gray-600 text-white focus:border-[#6D5D7B] disabled:bg-gray-800 disabled:border-gray-700' : 'border-gray-300 focus:border-[#4E4456] disabled:bg-gray-100 disabled:cursor-not-allowed'}`}> <option value="">Select Property Type</option> {propertyTypes.map((type) => (<option key={type} value={type}>{type}</option>))} </select> </div> {selectedPropertyType === 'Apartment' && buildings.length > 0 && ( <div> <label className={`block text-sm font-medium mb-1 ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>Building</label> <select value={selectedBuilding} onChange={handleBuildingChange} disabled={!selectedPropertyType} className={`w-full border rounded-md px-3 py-2 focus:outline-none focus:ring-2 focus:ring-[#4E4456]/50 ${darkMode ? 'bg-gray-700 border-gray-600 text-white focus:border-[#6D5D7B] disabled:bg-gray-800 disabled:border-gray-700' : 'border-gray-300 focus:border-[#4E4456] disabled:bg-gray-100 disabled:cursor-not-allowed'}`}> <option value="">Select Building</option> {buildings.map((building) => (<option key={building} value={building}>Building {building}</option>))} </select> </div> )} <div> <label className={`block text-sm font-medium mb-1 ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>Unit</label> <select value={selectedUnit} onChange={handleUnitChange} disabled={units.length === 0} className={`w-full border rounded-md px-3 py-2 focus:outline-none focus:ring-2 focus:ring-[#4E4456]/50 ${darkMode ? 'bg-gray-700 border-gray-600 text-white focus:border-[#6D5D7B] disabled:bg-gray-800 disabled:border-gray-700' : 'border-gray-300 focus:border-[#4E4456] disabled:bg-gray-100 disabled:cursor-not-allowed'}`}> <option value="">Select Unit</option> {units.map((unit) => (<option key={unit.id} value={unit.id}>{unit.unitNo} - {unit.type}</option>))} </select> </div> <div className="pt-4 flex gap-2"> <button onClick={calculateContribution} disabled={!selectedUnit || loading} className={`flex-1 bg-gradient-to-r from-[#4E4456] to-[#6D5D7B] text-white rounded-md py-2 px-4 font-medium hover:opacity-90 focus:outline-none focus:ring-2 focus:ring-[#4E4456]/50 disabled:opacity-50 disabled:cursor-not-allowed transition-all ${loading ? 'animate-pulse' : ''}`} > {loading ? ( <span className="flex items-center justify-center"> <svg className="animate-spin -ml-1 mr-2 h-4 w-4 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle className="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="4"></circle><path className="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg> Calculating... </span> ) : ( 'Calculate Contribution' )} </button> </div> </div> <div className={`mt-6 pt-4 border-t ${darkMode ? 'border-gray-700' : 'border-gray-200'}`}> <h4 className={`text-sm font-medium mb-2 ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>Reserve Fund Rates ({selectedYear})</h4> <div className="grid grid-cols-2 gap-x-4 gap-y-2"> <div className={`text-xs ${darkMode ? 'text-gray-400' : 'text-gray-600'}`}>Master Community: <span className={`ml-1 font-medium ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>OMR {getInflationAdjustedRates(selectedYear).masterCommunity.toFixed(2)}/m²</span></div> <div className={`text-xs ${darkMode ? 'text-gray-400' : 'text-gray-600'}`}>Typical Building: <span className={`ml-1 font-medium ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>OMR {getInflationAdjustedRates(selectedYear).typicalBuilding.toFixed(2)}/m²</span></div> <div className={`text-xs ${darkMode ? 'text-gray-400' : 'text-gray-600'}`}>Zone 3 (Al Zaha): <span className={`ml-1 font-medium ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>OMR {getInflationAdjustedRates(selectedYear).zone3.toFixed(2)}/m²</span></div> <div className={`text-xs ${darkMode ? 'text-gray-400' : 'text-gray-600'}`}>Zone 5 (Al Nameer): <span className={`ml-1 font-medium ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>OMR {getInflationAdjustedRates(selectedYear).zone5.toFixed(2)}/m²</span></div> <div className={`text-xs ${darkMode ? 'text-gray-400' : 'text-gray-600'}`}>Zone 8 (Al Wajd): <span className={`ml-1 font-medium ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>OMR {getInflationAdjustedRates(selectedYear).zone8.toFixed(2)}/m²</span></div> <div className={`text-xs ${darkMode ? 'text-gray-400' : 'text-gray-600'}`}>Staff Accommodation: <span className={`ml-1 font-medium ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>OMR {getInflationAdjustedRates(selectedYear).staffAccommodation.toFixed(2)}/m²</span></div> </div> <div className={`mt-2 text-xs italic ${darkMode ? 'text-gray-400' : 'text-gray-500'}`}>*Rates adjusted for 0.5% annual inflation from 2025 base year. Master Community rate shown as OMR equivalent.</div> </div> </div> <div className={`rounded-lg shadow-md p-6 ${darkMode ? 'bg-gray-800' : 'bg-white'}`}> <h3 className={`text-lg font-medium mb-4 ${darkMode ? 'text-[#AD9BBD]' : 'text-[#4E4456]'}`}>Calculation Results</h3> <Transition show={calculationResult !== null} enter="transition-opacity duration-300" enterFrom="opacity-0" enterTo="opacity-100" leave="transition-opacity duration-300" leaveFrom="opacity-100" leaveTo="opacity-0" unmount={false} > {calculationResult ? ( <div className="space-y-6 animate-fadeIn"> <div className="flex flex-col md:flex-row justify-between items-start md:items-center gap-4"> <div> <h4 className={`text-lg font-semibold ${darkMode ? 'text-white' : 'text-gray-800'}`}>{calculationResult.unitDetails.unitNo}</h4> <p className={`text-sm ${darkMode ? 'text-gray-400' : 'text-gray-600'}`}>{calculationResult.unitDetails.type}</p> </div> <div className="flex flex-col items-end"> <div className={`text-sm font-medium ${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>Annual Contribution ({calculationResult.year}):</div> <div className="text-xl font-bold text-[#4E4456] dark:text-[#AD9BBD]">OMR {calculationResult.totalAnnualContribution.toFixed(2)}</div> </div> </div> <div> <h4 className={`text-sm font-medium mb-2 ${darkMode ? 'text-gray-300' : 'text-gray-500'}`}>Property Details</h4> <div className={`rounded-md p-4 ${darkMode ? 'bg-gray-700' : 'bg-gray-50'}`}> <div className="grid grid-cols-2 gap-4"> <div><p className={`text-xs ${darkMode ? 'text-gray-400' : 'text-gray-500'}`}>Unit</p><p className={`text-sm font-medium ${darkMode ? 'text-white' : 'text-gray-800'}`}>{calculationResult.unitDetails.unitNo}</p></div> <div><p className={`text-xs ${darkMode ? 'text-gray-400' : 'text-gray-500'}`}>Type</p><p className={`text-sm font-medium ${darkMode ? 'text-white' : 'text-gray-800'}`}>{calculationResult.unitDetails.type}</p></div> <div><p className={`text-xs ${darkMode ? 'text-gray-400' : 'text-gray-500'}`}>Built-Up Area</p><p className={`text-sm font-medium ${darkMode ? 'text-white' : 'text-gray-800'}`}>{calculationResult.unitDetails.bua.toFixed(2)} sq.m.</p></div> <div><p className={`text-xs ${darkMode ? 'text-gray-400' : 'text-gray-500'}`}>Zone</p><p className={`text-sm font-medium ${darkMode ? 'text-white' : 'text-gray-800'}`}>{mockZones.find(z => z.id === selectedZone)?.name}</p></div> </div> </div> </div> <div> <h4 className={`text-sm font-medium mb-2 ${darkMode ? 'text-gray-300' : 'text-gray-500'}`}>Contribution Summary ({calculationResult.year})</h4> <div className={`rounded-md p-4 ${darkMode ? 'bg-gray-700' : 'bg-gray-50'}`}> <div className="space-y-2"> {calculationResult.masterContribution > 0 && (<div className="flex justify-between"><span className={`text-sm ${darkMode ? 'text-gray-300' : 'text-gray-600'}`}>Master Community:</span><span className={`text-sm font-medium ${darkMode ? 'text-white' : 'text-gray-800'}`}>OMR {calculationResult.masterContribution.toFixed(2)}</span></div>)} <div className="flex justify-between"><span className={`text-sm ${darkMode ? 'text-gray-300' : 'text-gray-600'}`}>{selectedZone === 'staff' ? 'Staff Accommodation:' : `Zone ${selectedZone}:`}</span><span className={`text-sm font-medium ${darkMode ? 'text-white' : 'text-gray-800'}`}>OMR {calculationResult.zoneContribution.toFixed(2)}</span></div> {calculationResult.buildingContribution > 0 && (<div className="flex justify-between"><span className={`text-sm ${darkMode ? 'text-gray-300' : 'text-gray-600'}`}>Building Specific:</span><span className={`text-sm font-medium ${darkMode ? 'text-white' : 'text-gray-800'}`}>OMR {calculationResult.buildingContribution.toFixed(2)}</span></div>)} <div className={`border-t pt-2 mt-2 ${darkMode ? 'border-gray-600' : 'border-gray-200'}`}> <div className="flex justify-between font-medium"><span className={`text-sm ${darkMode ? 'text-gray-300' : 'text-gray-800'}`}>Total Annual Contribution:</span><span className="text-sm text-[#4E4456] dark:text-[#AD9BBD]">OMR {calculationResult.totalAnnualContribution.toFixed(2)}</span></div> <div className="flex justify-between text-sm mt-1"><span className={`${darkMode ? 'text-gray-400' : 'text-gray-500'}`}>Monthly Payment:</span><span className={`${darkMode ? 'text-gray-300' : 'text-gray-700'}`}>OMR {(calculationResult.totalAnnualContribution / 12).toFixed(2)}</span></div> </div> </div> </div> </div> <div> <h4 className={`text-sm font-medium mb-2 ${darkMode ? 'text-gray-300' : 'text-gray-500'}`}>Contribution Breakdown (Approx.)</h4> <div className={`rounded-md p-4 ${darkMode ? 'bg-gray-700' : 'bg-gray-50'}`}> <div className="space-y-4"> {calculationResult.componentBreakdown.map((category, categoryIndex) => ( <div key={categoryIndex}> <h5 className={`text-xs font-medium mb-2 ${darkMode ? 'text-gray-300' : 'text-gray-600'}`}>{category.category}</h5> <div className="space-y-1"> {category.components.map((component, componentIndex) => ( <div key={componentIndex} className="flex justify-between text-xs"> <span className={darkMode ? 'text-gray-400' : 'text-gray-600'}>{component.name}</span> <span className={`font-medium ${darkMode ? 'text-white' : 'text-gray-800'}`}> OMR {component.share.toFixed(2)} </span> </div> ))} </div> {categoryIndex < calculationResult.componentBreakdown.length - 1 && (<div className={`border-t mt-2 ${darkMode ? 'border-gray-600' : 'border-gray-200'}`} />)} </div> ))} </div> </div> </div> <div className="flex justify-end space-x-3 pt-2"> <button onClick={generatePDFReport} className={`px-4 py-2 text-sm font-medium rounded-md ${darkMode ? 'bg-gray-700 text-white hover:bg-gray-600' : 'bg-gray-100 text-gray-800 hover:bg-gray-200'} transition-colors`}> <span className="flex items-center"><svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M7 21h10a2 2 0 002-2V9.414a1 1 0 00-.293-.707l-5.414-5.414A1 1 0 0012.586 3H7a2 2 0 00-2 2v14a2 2 0 002 2z" /></svg>Generate PDF</span> </button> <button className="px-4 py-2 text-sm font-medium rounded-md bg-gradient-to-r from-[#4E4456] to-[#6D5D7B] text-white hover:opacity-90 transition-colors"> <span className="flex items-center"><svg className="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M8 7H5a2 2 0 00-2 2v9a2 2 0 002 2h14a2 2 0 002-2V9a2 2 0 00-2-2h-3m-1 4l-3 3m0 0l-3-3m3 3V4" /></svg>Save Calculation</span> </button> </div> <div className={`text-xs italic ${darkMode ? 'text-gray-400' : 'text-gray-500'}`}> <p>* Calculation based on {calculationResult.year} rates adjusted for 0.5% annual inflation since 2025 base year.</p> <p>* Component breakdown is an approximation based on the 2021 Reserve Fund Study.</p> <p>* Master Community contribution shown as OMR equivalent; actual billing may differ.</p> </div> </div> ) : ( <div className={`flex flex-col items-center justify-center py-12 text-center ${darkMode ? 'text-gray-500' : 'text-gray-400'}`}> <svg className={`w-16 h-16 mb-4 ${darkMode ? 'text-gray-600' : 'text-gray-300'}`} fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={1.5} d="M9 7h6m0 10v-3m-3 3h.01M9 17h.01M9 14h.01M12 14h.01M15 11h.01M12 11h.01M9 11h.01M7 21h10a2 2 0 002-2V5a2 2 0 00-2-2H7a2 2 0 00-2 2v14a2 2 0 002 2z" /></svg> <p className="text-sm">Select a property from the form to calculate the reserve fund contribution.</p> </div> )} </Transition> </div> </div> </div> );
};


// --- Helper Components ---

// KPI Card Component
const KpiCard = ({ title, value, description, trend, icon, compactView, darkMode }) => {
  return ( <div className={`overflow-hidden shadow-md rounded-lg transform transition-transform hover:scale-[1.02] ${darkMode ? 'bg-gray-800' : 'bg-white'}`}> <div className={`p-5 ${compactView ? 'p-4' : 'p-5'}`}> <div className="flex items-center justify-between"> <div className="flex-1"> <h3 className={`text-sm font-medium ${darkMode ? 'text-gray-300' : 'text-gray-500'}`}>{title}</h3> <div className="mt-1 flex items-baseline"> <p className={`${compactView ? 'text-xl' : 'text-2xl'} font-semibold ${darkMode ? 'text-white' : 'text-[#4E4456]'}`}>{value}</p> {trend && ( <span className={`ml-2 flex items-center text-xs font-medium ${trend === 'up' ? 'text-green-600 dark:text-green-400' : 'text-red-600 dark:text-red-400'}`}> {trend === 'up' ? ( <svg className="self-center flex-shrink-0 h-4 w-4 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M5 10l7-7m0 0l7 7m-7-7v18" /></svg>) : ( <svg className="self-center flex-shrink-0 h-4 w-4 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M19 14l-7 7m0 0l-7-7m7 7V3" /></svg>)} </span> )} </div> <p className={`mt-1 text-xs ${darkMode ? 'text-gray-400' : 'text-gray-500'}`}>{description}</p> </div> <div className={`rounded-md p-3 ${darkMode ? 'bg-[#4E4456]/30 text-[#AD9BBD]' : 'bg-[#4E4456]/10 text-[#4E4456]'}`}> {icon} </div> </div> </div> </div> );
};

// Chart Card Component (Wrapper for charts)
const ChartCard = ({ title, children, darkMode }) => {
  return ( <div className={`overflow-hidden shadow-md rounded-lg transform transition-all hover:shadow-lg ${darkMode ? 'bg-gray-800' : 'bg-white'}`}> <div className={`px-6 py-4 border-b ${darkMode ? 'border-gray-700' : 'border-gray-200'}`}> <h3 className={`text-lg font-medium ${darkMode ? 'text-[#AD9BBD]' : 'text-[#4E4456]'}`}>{title}</h3> </div> <div className="p-4"> {children} </div> </div> );
};

// --- Global Styles Component ---
// Includes simple animation styles
const GlobalStyles = () => {
  const isDarkMode = document.documentElement.classList.contains('dark'); const trackColor = isDarkMode ? '#1f2937' : '#f1f1f1'; const thumbColor = isDarkMode ? '#4b5563' : '#888'; const thumbHoverColor = isDarkMode ? '#6b7280' : '#555';
  return ( <style>{` @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } } .animate-fadeIn { animation: fadeIn 0.5s ease-out forwards; } .dark { color-scheme: dark; } ::-webkit-scrollbar { width: 8px; height: 8px; } ::-webkit-scrollbar-track { background: ${trackColor}; border-radius: 10px; } ::-webkit-scrollbar-thumb { background: ${thumbColor}; border-radius: 10px; } ::-webkit-scrollbar-thumb:hover { background: ${thumbHoverColor}; } `}</style> );
};


// Export the main App component
export default App;
